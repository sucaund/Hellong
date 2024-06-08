# HELLONG (위치 기반 헬스장 검색 및 커뮤니티 사이트)

이 프로젝트는 위치 기반 헬스장 검색 및 커뮤니티 사이트입니다. 일반 회원과 헬스장 회원으로 구분되며, 주요 기능은 다음과 같습니다:

### 일반 회원 기능
- 현재 위치를 기반으로 주변 헬스장의 위치, 가격, 리뷰 등의 정보 제공
- 헬스장 회원권 거래 및 커뮤니티 게시판 (운동 정보, 바디 프로필 등) 이용

### 헬스장 회원 기능
- 회원권 판매 및 홍보 페이지 관리
- 트레이너 및 헬스장 상세 정보 작성

이 사이트를 통해 사용자들은 보다 합리적인 선택을 할 수 있습니다.

# 개발환경

![스크린샷 2024-05-16 115305](https://github.com/sucaund/Hellong/assets/139835601/60dbc9f3-a8de-4d04-8058-b6973c018bb4)

# DB설계

![Untitled](https://github.com/sucaund/Hellong/assets/139835601/5003cead-ca13-465c-baac-e37ef7bcdc5b)


# 작업 구성도

![hellong0608](https://github.com/sucaund/Hellong/assets/139835601/84f99c6b-b43d-4570-9133-0f5fabf74ab3)

# 트러블슈팅

### ㅇㅇㅇ
![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6243e06e-9f36-41a2-88a0-71adfdc432d7/ef2f4c86-451d-4b14-81c0-3cb8da5de24f/Untitled.png)

ajax를 통해 회원정보를 가져오는과정에서 반환값이 null로서 전달되지않아 오류가 생겼다.

이유는?
반환값이 **`null`**인 상황은 **`session.selectOne("getGym", g_id);`** 호출이 예상대로 작동하지 않았음

```java
@Override
	public Gym getGym(int g_id) {
		System.out.println("QBoarddaoImpl getGym g_id"+"   "+g_id);
		Gym gym = null;
		try {
			session.selectOne("getGym", g_id);
		} catch (Exception e) {
			System.out.println("QBoarddaoImpl getGym e.getMessage()->" + e.getMessage());
		}
		System.out.println("QBoarddaoImpl sucess! gym"+ " "+ gym);
		return gym;
	}
```

