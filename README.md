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

### SyntaxError: Unexpected end of JSON input

![Untitled (1)](https://github.com/sucaund/Hellong/assets/139835601/284e2c8a-3b53-400f-877f-c779b16a99f3)


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


해결: 창피하지만 기초적인 실수로서 반환값을 설정하지 않았음...
```java
@Override
	public Gym getGym(int g_id) {
		System.out.println("QBoarddaoImpl getGym g_id"+"   "+g_id);
		Gym gym = null;
		try {
			gym= session.selectOne("getGym", g_id);
		} catch (Exception e) {
			System.out.println("QBoarddaoImpl getGym e.getMessage()->" + e.getMessage());
		}
		System.out.println("QBoarddaoImpl sucess! gym"+ " "+ gym);
		return gym;
	}
```



### ajax로 이미지 경로 가져오기 오류(경로설정의 개념학습)

문제= 데이터의 이름을 가져온후 해당 데이터의 경로+데이터 이름을 통한 src=”url”호출을 시도햇으나  not found 오류가나왔다.

```jsp
$('.GymEdit-btn').on('click',function() {
				var memberId = $(this).data('memberid');
				// AJAX를 사용하여 서버에 체육관 정보를 요청합니다.
				$.ajax({
					url : 'getGymDetails', // 서버의 URL을 지정해야 합니다.
					type : 'GET',
					data : {
						'id' : memberId
					}, // 서버로 보낼 데이터
					dataType : 'json', // 응답 받을 데이터의 타입
					success : function(data) {
						console.log(data);
						var imageUrl = '/upload/' + data.g_document;
                        //외부경로를 가져올때 WebConfig에서 설정한 웹 url관련 @configuration 설정에서
                       서버내 파일 경로와 해당 경로를 호출하는 매핑/upload/를 설정한다...
						// 성공 시 모달의 필드에 데이터를 채웁니다.
						// 모달의 각 입력 필드에 ID를 설정하고 해당 ID를 사용하여 값을 설정합니다.
						$('#Gym_Content input[name="m_number"]').val(data.m_number);//파트너 번호
						$('#Gym_Content input[name="g_name"]').val(data.g_name);//상호명
						$('#Gym_Content input[name="g_address"]').val(data.g_address);//주소
						$('#Gym_Content input[name="g_tel"]').val(data.g_tel);//전화번호
						$('#Gym_Content input[name="g_companynumber"]').val(data.g_companynumber);//사업자 등록번호
						$('#Gym_Content img[name="g_document"]').attr('src', imageUrl);//사업자 등록증
						// 여기에 다른 필드에 대한 데이터 설정을 추가합니다.
						 if(data.common_mcd === 10) { // 대기
						        $('#Gym_Content input[name="GymOpen_mcd"][value="10"]').prop('checked', true);
						    } else if(data.common_mcd === 20) { // 승인
						        $('#Gym_Content input[name="GymOpen_mcd"][value="20"]').prop('checked', true);
						    } else if(data.common_mcd === 30) { // 노출
						        $('#Gym_Content input[name="GymOpen_mcd"][value="30"]').prop('checked', true);
						    }
						// 모달을 표시합니다.
						$('#Gym_Content').modal('show');
					},
					error : function(xhr, status, error) {
						// 오류 처리
						console.error("AJAX Error: " + status
								+ error);
					}
				});
			});
});
```
상단 코드의 매핑과  url링크를  설정하기위해 @configuration 클래스를 추가한다.


```java
package com.oracle.hellong.configuration;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {
    private String resourcePath = "file:///C:/Users/admin/git/j_team_projects/hellong/uploads/";// 실제 파일 저장 경로 
    private String uploadPath = "/upload/**"; // view에서 사용할 경로


    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/upload/**").addResourceLocations("file:///C:/Users/admin/git/j_team_projects/hellong/uploads/");
    }
```

