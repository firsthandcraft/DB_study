
# ✏️ 신청폼과 공유

![20230907_162117](https://github.com/firsthandcraft/DB_study/assets/97497153/1b52be32-c136-422b-878f-703ccf6c1d73)

ERD 에서 관계 표현  정리

---
## 고려사항

###  공유하기 기능을 할때 반드시 관리자를 통해서만 공유 권한을 줄것인지 
    

001. 관리자만 권한부여

- 공유하는업무를 할경우 관리자가 전부 공유를  해줘야한다.     
    
002. 관리자, 제작자 권한부여
- 읽기, 수정, 삭제 에 대한 권한분류가 필요하다. 
  - (삭제는 관리자, 제작자만 가능)
    
- 이직, 사퇴를 할경우 (관리자가 다른이에게 owner권한 부여가능(삭제,수정) 하게)
- 제작자는 본인 부서 내 사람들만 권한부여 가능(읽기, 수정) // 관리자는 다른 부서에게 모든 권한 부여 가능
  ![formmenu2](https://github.com/firsthandcraft/DB_study/assets/97497153/b0e3d110-e42e-4dfd-8527-3ae1e45b5c15)


  - 권한?
    - 읽기 ->> 질문의 사항과 응답 결과를 볼수 있다.
    - 수정 ->> 질문의 사항을 수정하고 응답 결과에 대한 수정이 가능하다. 
![Group 23](https://github.com/firsthandcraft/DB_study/assets/97497153/ac5fd252-1cb0-4b61-956d-259eff5116c2)

### 신청폼을 나만보기로 하고싶을 경우 
- 게시 상태를 (작성중 / 게시 / 철회) 로 두어 작성중일때만 나만보기로 설정
- 공유테이블에서 작성자만 있게 
![formmenu](https://github.com/firsthandcraft/DB_study/assets/97497153/d9ee3825-f53e-495c-861e-c4f38d8280f7)


### 프로세스 
- 사업 등록 후에 폼을 작성하는것이 아닌 (사업을 등록하는 직원과 프로그램 신청작성하는 직원이 다를수도 있다.)
- 연결하는 방식으로 하는것을 추천 (사업 등록시에 신청폼을 연결하는 방식으로)
![formprocess](https://github.com/firsthandcraft/DB_study/assets/97497153/9314afc3-3061-4707-ace0-4a9aa11b0ddf)

---

![20230907_161925](https://github.com/firsthandcraft/DB_study/assets/97497153/f082c1ce-e253-4b5a-ba42-b2a49fe117e6)






