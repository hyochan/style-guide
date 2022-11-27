
## 데이터 설계 원칙


### 1. Relation 필드는 id 대신 ref를 사용

```sh
users
- <userID>

companies
- <companyID>

discussions
- <discussionID>
	- companyRef
	- userRef

```

### 2. Ref를 추론할 수 있으면 domain을 생략

```sh
myCompanies
- <companyID>
  - ref
```

### 3. 컬랙션이 여러 상위 컬랙션 도큐먼트를 참조해야하는 경우 최상위 컬랙션으로 생성
```sh
// 한 회사의 댓글만 보면 되는 경우
companies
- <compnyID>
	- replies
		- <replyID>
		    - id
		    - text
		    - createdAt

// 여러 회사의 댓글을 봐야하는 경우
- replies
	- <replyID>
		- id
		- text
		- createdAt
		- companyRef
```
> 예를들어 만약 회사 (companies) 안에 댓글이 들어간다고 하면 `companies/<companyID>` 도큐먼트 아래 `replies` 컬랙션이 들어간다고 생각할 수 있습니다.
> 하지만 만약 여러 회사들의 댓글을 모아보는 시나리오가 있다면 최상위 컬랙션으로 빼야 검색을 원활하게 할 수 있습니다.
> 만약 한 회사에 댓글에 대해서만 검색을 한다면 `companies/<companyID>/replies` 와 같이 nested하게 컬랙션을 생성해야합니다.

### 4. 좋아요 등으로 도큐먼트를 복사해가는 경우 복사한 쪽에서 원본 `ref`를 추가
```sh
- users
	- <userID>
		- followers
			- <followerID>
				- ref // <= 추가
		- id
		- displayName
		- birthday
```
> 위와 같이 `ref`를 복사해가면서 마스터 도큐먼트를 알아낼 수 있습니다. 이후에 서버에서 짠 로직으로 데이터를 최신 상태로 동기화하는데도 도움이 됩니다.

### 5. 여러 `ref`를 참조해야하는 경우 도메인을 추가
```sh
- discussions
	- <discussionID>
		- companyRef
		- userRef
```
> 여러 `ref`가 존재하는 경우 도메인 `prefix`를 추가합니다. 다만, 복사해 도큐먼트면 `ref`가 존재할 것입니다. 대부분 `ref`가 있다면 백업을 해야하는 도큐먼트일 것입니다.

### 6. Soft Delete 컬랙션
데이터 삭제 시기를 어플리케이션 관리자가 제어하고자 한다면 [soft deletion 전략](https://en.wiktionary.org/wiki/soft_deletion)이 필요합니다. 그럴 경우는 아래와 같이 `createdAt`, `updatedAt` 그리고 `deletedAt`을 꼭 추가해주세요.
```sh
- discussions
	- <discussionID>
		- createdAt
		- updatedAt
		- deletedAt
```

