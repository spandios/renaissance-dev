# Decisions - 중요한 결정 기록

_"왜 이렇게 했지?"를 6개월 후에도 알 수 있도록_

---

## 🔗 공식 ADR (Git 레포)

**beauty-sale 프로젝트의 공식 ADR**은 Git 레포에서 관리:
- **경로**: `/Users/dev_heo/Code/beauty-sale/docs/decisions/`
- **형식**: 번호 체계 (001, 006-024 등)
- **용도**: 팀과 공유하는 공식 아키텍처 결정

**최근 주요 ADR (2026-03-21 기준):**
- [024-comparison-value-product-scope-and-core-target.md](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/024-comparison-value-product-scope-and-core-target.md)
- [023-coupon-calculator-modal-redesign.md](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/023-coupon-calculator-modal-redesign.md)
- [022-price-comparison-guide-copy-and-normal-band.md](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/022-price-comparison-guide-copy-and-normal-band.md)
- [021-coupon-mvp-structured-common-and-manual-local-profile.md](file:///Users/dev_heo/Code/beauty-sale/docs/decisions/021-coupon-mvp-structured-common-and-manual-local-profile.md)

**ADR 전체 목록 보기:**
```bash
ls -lt /Users/dev_heo/Code/beauty-sale/docs/decisions/
```

---

## 📝 Personal Decisions (여기)

**이 폴더의 용도:**
- 🧪 **실험과 고민**: 아직 확정되지 않은 시도
- 💭 **개인 메모**: "왜 이렇게 했지?" 개인 컨텍스트
- 🔬 **빠른 스케치**: 공식 ADR 전 단계 기록
- 🎯 **비프로젝트 결정**: beauty-sale 외 개인 기술 선택

**워크플로우:**
1. 기술 선택 고민 시작 → 여기에 메모
2. 실험, 조사, 트레이드오프 분석
3. 결정 확정 → Git 레포에 공식 ADR 작성
4. 여기 메모에 "✅ ADR-XXX로 공식화" 링크 추가

---

## 📌 결정 기록 원칙

**언제 기록하나?**
- 새로운 라이브러리 시도
- 아키텍처 실험
- 개인 학습 프로젝트 기술 선택
- 부업/사이드 프로젝트 결정
- 커리어 관련 선택 (이직, 학습 방향 등)

**어떻게 기록하나?**
- 파일명: `YYYY-MM-DD-간단한-제목.md`
- 형식: 자유 (부담 없이!)
- 나중에 공식 ADR로 승격 가능

---

## 📝 템플릿

새 결정을 기록할 때:

```markdown
# [결정 제목]

Date: YYYY-MM-DD
Status: Proposed / Accepted / Deprecated / Superseded

## Context (배경)
왜 이 결정이 필요했나?
어떤 문제를 해결하려고 했나?

## Decision (결정)
무엇을 선택했나?
어떤 대안이 있었나?

## Consequences (결과)
이 결정의 장단점은?
앞으로 어떤 영향을 미칠까?

## Alternatives Considered (고려한 대안)
- 대안 1: 장단점
- 대안 2: 장단점

## References
- 관련 링크
- 참고 자료
```

---

## 🔍 최근 결정

_최근 3개월 내 중요한 결정을 여기에 링크_

- TODO: 최근 결정 추가

---

_좋은 결정은 미래의 나를 돕고, 기록된 결정은 팀을 돕는다_
