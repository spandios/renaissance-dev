# Beauty-Sale 기술 컨텍스트

_Beauty-Sale 어드바이저가 빠르게 참조할 핵심 기술 문서 요약_

Last updated: 2026-03-23

---

## 📋 빠른 참조

**Git 레포**: `/Users/dev_heo/Code/beauty-sale/`

**핵심 문서**:
- [PRICE_CALCULATION.md](file:///Users/dev_heo/Code/beauty-sale/docs/PRICE_CALCULATION.md) - 가격 계산 로직
- [DB_SCHEMA.md](file:///Users/dev_heo/Code/beauty-sale/docs/DB_SCHEMA.md) - 데이터베이스 구조
- [DESIGN_GUIDE.md](file:///Users/dev_heo/Code/beauty-sale/docs/DESIGN_GUIDE.md) - UI/UX 가이드
- [decisions/](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/) - ADR 문서 (20개+)

---

## 🎯 제품 전략 (ADR-024)

**핵심 타깃**: 20대 후반~30대 여성

**상품 선정 기준**:
- 1차: 정가/대표 판매가 **2만원 이상** 중심
- 예외: 2만원 미만이라도 **비교 가치**가 있으면 포함
  - 반복구매성 높음
  - 번들/증정/기획세트 차이 큼
  - 플랫폼별 쿠폰/구성 차이로 실구매 판단 달라짐

**포지셔닝**: 
- ❌ "최저가 찾기 앱"
- ✅ "지금 어디서 사는 게 가장 이득인지 판단해주는 도구"

**우선 상품군**:
- 색조: 2만~4만원대 (쿠션, 파운데이션, 팔레트, 프리미엄 립)
- 스킨케어: 1만 후반~3만원대 반복구매 품목 (선크림, 토너패드, 앰플, 크림, 대용량 클렌징/기획세트)

---

## 💰 가격 계산 로직 (핵심)

### 유효가 (Effective Price)
```
effectivePrice = salePrice / max(bundleQty, 1)
```

### 평소 최저가 (Typical Baseline)
- **데이터 소스**: 최근 30일 `product_market_points`
- **downsample**: 같은 hour 중복 제거 (가장 늦은 관측 1개만)
- **준비 조건 (`typicalReady`)**:
  - downsample 후 포인트 수 >= 6
  - 관측 일수 (distinct day) >= 3
- **Typical 가격**: downsample 가격들의 **중앙값 (median)**

### 티어 판정 (Decision State)
- **임계값**: `threshold = max(round(median * 0.07), 1)` (7%)
- **차이값**: `deltaFromTypical = typical - currentEffectivePrice`
- **판정**:
  - `delta >= threshold` → `BUY` (평소보다 저렴)
  - `delta <= -threshold` → `WATCH` (평소보다 비쌈)
  - 그 외 → `NORMAL`

### Historical Low 보조 신호
- **meaningfulGap**: `max(round(typicalEffectivePrice * 0.03), 300)`
- **isHistoricalLowest**: 
  - `typicalReady && current <= historicalMin`
  - `(typical - historicalMin) >= meaningfulGap`
- **isRecentWindowLow** (legacy 필드명, 실제 의미: historical near low):
  - `current > historicalMin`
  - `(diffWon <= 3000 || diffPct <= 10.0)`
  - `(typical - historicalMin) >= meaningfulGap`

### 안정 SKU 완충
- 분포 기준: `p90 - p10 <= median * 0.03`
- 최근 14일: `max - min <= median * 0.01`
- 안정 SKU면:
  - `isRecentWindowLow = false`
  - `isHistoricalLowest = false`
  - `abs(delta) < threshold * 2` 범위에서 `BUY/WATCH` → `NORMAL` 클램프

---

## 🗄️ 데이터베이스 구조 (핵심)

### 핵심 테이블

**상품 계층**:
```
brands (1:N) products (1:N) platform_products (1:N) price_snapshots
```

**가격 요약**:
- `product_deal_summaries`: 리스트/상세 사전 계산 캐시
  - `current_best_price`, `current_effective_price`
  - `min_effective_price`, `is_window_low`
  - `tracking_days`, `data_points`

**시장 포인트**:
- `product_market_points`: 특정 시점 "시장 전체 최저 유효가" 시계열

**이벤트/혜택**:
- `sale_events`: 플랫폼/브랜드 이벤트
  - `discount_percent`, `max_discount_amount`

**관계**:
- `product_relations`: 같은 상품의 다른 구성 (세트 등)

---

## 🎨 UI/UX 원칙

**디자인 철학**:
- 가격 비교 기준: "할인율"보다 **"혜택가/가격 시그널"** 우선
- 관측 데이터 신뢰 시그널 노출 (`trackingDays`, `updatedAt`, 최저 대비 delta)

**컬러 토큰** (핵심):
- 브랜드: `--color-coral: #e8627c`
- 플랫폼:
  - OliveYoung: `#3d8a5a`
  - Coupang: `#e8627c`
  - Musinsa: `#4a7fd4`

**가격 시그널 분류** (`frontend/lib/price-signal.ts`):
- `lowest`: `diffWon <= 10` && `diffPct <= 0.2`
- `near_lowest`: `diffWon > 10` && (`diffWon <= 3000` || `diffPct <= 10`)
- `normal`: 그 외

**플랫폼 정렬 우선순위** (`frontend/lib/platform.ts`):
- 증정 승격 임계값: `GIFT_PROMOTE_RATIO = 1.05` (5%)
- 절대값 보정: `GIFT_PROMOTE_ABS = 2000`

---

## 🔧 쿠폰 예상가 계산 (MVP)

**핵심 규칙**:
- 쿠폰 이벤트: `eventType=DISCOUNT`로만 판별
- 할인 계산: 구조화 필드(`discountPercent`, `maxDiscountAmount`)만 사용
- ❌ `title`/`discountText` 문자열 파싱 금지

**적용 우선순위**:
- `MANUAL` (사용자 수동 %) > `EVENT` (공통 이벤트) > `NONE`

**계산식**:
```kotlin
discount = round(baseSalePrice * ratePercent / 100)
if (maxDiscountAmount != null) {
  discount = min(discount, maxDiscountAmount)
}
estimatedPrice = baseSalePrice - discount
```

**저장** (MVP):
- `localStorage` (`beautysale.couponPrefs.v1`)

---

## 📊 주요 지표

**Deal Summary 계산**:
- `trackingDays`: 상품 첫 스냅샷 날짜 ~ 오늘
- `dataPoints`: `sale_price IS NOT NULL` 스냅샷 개수
- 추적 30일 이상: 최근 30일 윈도우에서 min
- 30일 미만: 전체 구간 min

**준비 조건** (프론트엔드 문장):
- `trackingDays >= 7`
- `dataPoints >= 7`

---

## 🚀 기술 스택

**Backend**:
- Spring Boot, Kotlin, JPA
- DB: PostgreSQL

**Frontend**:
- React, TypeScript, Next.js
- Styling: Tailwind CSS (custom design tokens)

**Analytics**:
- PostHog (진행 중)
- GA4 (예정)

**배포**:
- Docker Compose (홈서버)
- 재시작 스크립트: `./scripts/homeserver_stack.sh restart`

---

## 📝 최근 업데이트 (2026-03-23)

**Git Pull 결과**:
- `DealPolicy.kt` 수정
- `DESIGN_GUIDE.md` 업데이트
- `FRONTEND_COMPONENT_MAP.md` 대폭 확장
- `DB_SCHEMA.md` 업데이트
- 프론트엔드 테스트 수정

---

## 🔗 참고 링크

**ADR 주요 문서**:
- [ADR-024: 비교 가치 중심 상품 스코프와 핵심 타깃](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/024-comparison-value-product-scope-and-core-target.md)
- [ADR-023: 쿠폰 계산기 모달 재설계](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/023-coupon-calculator-modal-redesign.md)
- [ADR-022: 가격 비교 가이드 기준](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/022-price-comparison-guide-copy-and-normal-band.md)

**전체 ADR 목록**:
```bash
ls -lt /Users/dev_heo/Code/beauty-sale/docs/decisions/
```

---

_이 문서는 beauty-sale 레포의 핵심 문서를 요약한 것입니다._
_최신 내용은 항상 Git 레포의 원본 문서를 참조하세요._
