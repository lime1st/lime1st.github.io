---
{"dg-publish":true,"permalink":"/illiterate//db-index/","tags":["DB","index"],"noteIcon":"","created":"2025-03-06T23:13:00","updated":"2025-03-12T02:55:23+09:00"}
---

**데이터베이스 인덱싱** : 데이터베이스 인덱싱은 조회 성능을 크게 향상시킬 수 있는 방법이다. 인덱스를 사용하면 데이터베이스는 데이터를 빠르게 검색할 수 있습니다. 올바르게 설계된 인덱스는 읽기 성능을 최적화하고 쿼리 응답 시간을 줄입니다. 주의할 점은 인덱스가 너무 많으면 쓰기 성능이 저하될 수 있다는 것입니다.

데이터베이스 인덱싱은 데이터베이스 성능을 향상시키기 위해 사용되는 기술로, 특정 데이터 항목에 대한 빠른 접근을 가능하게 합니다. 인덱스는 ==책의 색인==처럼 작동하여, 데이터베이스 내의 데이터를 효율적으로 검색할 수 있도록 돕습니다.

## 인덱스의 주요 개념

1. **인덱스 구조**: 인덱스는 일반적으로 B-트리(B-tree)나 해시 테이블(hash table)과 같은 데이터 구조를 사용하여 구현됩니다. B-트리는 대량의 데이터를 빠르게 검색할 수 있도록 균형을 유지하는 구조입니다.

2. **인덱스의 종류**:
   - **클러스터드 인덱스(Clustered Index)**: 데이터가 인덱스의 순서와 동일한 순서로 저장됩니다. 각 테이블에는 하나의 클러스터드 인덱스만 가질 수 있습니다.
   - **논클러스터드 인덱스(Non-Clustered Index)**: 인덱스와 실제 데이터가 별도로 저장됩니다. 여러 개의 논클러스터드 인덱스를 테이블에 만들 수 있습니다.
   - **유니크 인덱스(Unique Index)**: 중복되지 않는 값을 보장하는 인덱스입니다. 주로 기본 키(Primary Key)와 함께 사용됩니다.

3. **인덱스의 장점**:
   - **빠른 검색**: 인덱스를 사용하면 특정 데이터를 더 빠르게 검색할 수 있습니다.
   - **정렬 및 필터링 효율성**: 인덱스는 정렬된 데이터를 제공하여 쿼리의 정렬 및 필터링 성능을 향상시킵니다.

4. **인덱스의 단점**:
   - **저장 공간**: 인덱스를 생성하면 추가적인 저장 공간이 필요합니다.
   - **쓰기 성능 저하**: 데이터 삽입, 수정, 삭제 시 인덱스도 업데이트해야 하므로, 쓰기 성능이 저하될 수 있습니다.

### 인덱스 사용 시 고려사항

- **쿼리 패턴**: 자주 사용되는 쿼리와 필터링 조건에 따라 인덱스를 설계합니다.
- **데이터 변경 빈도**: 데이터가 자주 변경되는 경우, 인덱스의 유지 관리 비용을 고려해야 합니다.
- **인덱스의 수**: 너무 많은 인덱스를 생성하면 오히려 성능이 저하될 수 있습니다.

인덱싱은 데이터베이스 성능을 최적화하는 데 중요한 역할을 하며, 적절한 인덱스를 설계하는 것이 중요합니다. 

---
[[Illiterate/내일배움캠프/isolation level\|isolation level]]
[[Illiterate/내일배움캠프/Transaction scope persistence context\|Transaction scope persistence context]]