# Board
Spring Framework를 기반으로 한 CRUD 게시판 제작

![index](https://user-images.githubusercontent.com/42952319/103534254-e3170480-4ed1-11eb-8ca1-3b2f5615dbce.PNG)
![get](https://user-images.githubusercontent.com/42952319/103534287-ee6a3000-4ed1-11eb-8187-2d7732ce0da8.PNG)
![get_modal](https://user-images.githubusercontent.com/42952319/103534304-f5913e00-4ed1-11eb-9edf-a32121ec5a1d.PNG)



## 개발 언어 및 환경
- bootstrap(http://startbootstrap.com/template-overviews/sb-admin-2/)
- sts v3.9.13
- java v1.8
- junit v4.12
- lombok v1.18.0
- HikariCP v2.7.8
- oracle database 11g
- OJDBC8
- mybatis v3.4.6
- mybatis-spring v1.3.2
- log4jdbc v1.16

## SQL Query
- tb1_board

create sequence seq_board;

create table tb1_board(
	bno number(10,0),
	title varchar2(200) not null,
	content varchar2(2000) not null,
	writer varchar2(50) not null,
	regdate date default sysdate,
	updatedate date default sysdate
);

alter table tb1_board add constraint pk_board
primary key(bno);

insert into tb1_board(bno, title, content, writer)
values(SEQ_BOARD.nextval, 'test title', 'test content', 'user');

- tb1_reply

create table tb1_reply (
rno number(10,0),
bno number(10,0) not null,
reply varchar2(1000) not null,
replyer varchar2(50) not null,
replydate date default sysdate,
updatedate date default sysdate
);

create sequence seq_reply;

alter table tb1_reply add constraint pk_reply primary key (rno);

alter table tb1_reply add constraint fk_reply_board
foreign key(bno) references tb1_board(bno);

- tb1_attach

create table tb1_attach (
    uuid varchar2(100) not null,
    uploadPath varchar2(200) not null,
    fileName varchar2(100) not null,
    fileType char(1) default 'I',
    bno number(10,0)
);

alter table tb1_attach add constraint pk_attach primary key (uuid);

alter table tb1_attach add constraint fk_board_attach foreign key (bno)
references tb1_board(bno);


##
### -1-

데이터베이스 설계
pom.xml
변경
- java version 1.8
- junit 4.12
- javax.sevlet-api 3.1.0
- maven 관련 java version 1.8
추가
- spring framework(test, jdbc, tx)
- HikariCP
- MyBatis
- mybatis-spring
- Log4jdbc
- lombok
- maven-war-plugin

root-context
- db정보 입력


### -2-
추가
- RootConfig
- Servletconfig
- WebConfig
- BoardVO
- BoardMapper(interface)
- BoardMapperTests

* 지원되지 않는 문자 집합(클래스 경로에 orai18n.jar 추가): KO16MSWIN949
	에러로 인해 orai18n 추가


### -3-
추가
- BoardService(interface)
- BoardServiceImpl
- BoardServiceTests(CRUD)


- BoardMapperTests(CRUD) 기능 구현
- BoardServiceTests(CRUD) 기능 구현


### -4-
- Controller 부분 추가(BoardController.java)
- ControllerTest 부분 추가(BoardMapperTests.java)
- Controller 부분에서의 CRUD 추가 및 테스트

### BBS_1
- modal 창 추가
- 각 페이지 밑에 버튼 추가
- Citeria(페이징 처리) 추가
- 각 페이지에 pageNum, amount 값 추가

### BBS_2
- Citeria(검색 처리) 추가
#### list.jsp
- 검색창 추가 및 검색 처리
- 파라미터를 url로 넘겨 list에 클릭시 전에 보던 페이지로 이동하게 만듬

### BBS_3
#### comment CRUD 추가
- ReplyMapper.java
- ReplyMapper.xml
- ReplyVO.java
#### comment mapper Tests 추가
- ReplyMapperTests.java
#### comment service 단 추가
- ReplyService.java
- ReplyServicelmpl.java

### BBS_4
#### ReplyController.java 추가
- comment(댓글) CRUD처리
#### javascript 모듈 구성
- reply.js(add, getList, remove, update, get, displyTime) 추가
#### get.jsp
- get(개시글) 화면에 comment(댓글) 구현
- new reply 버튼 추가
- modal 화면 추가

### BBS_5
#### modal CRUD 구현
- new reply 버튼 이벤트 구현
- modal창 modify, remove 버튼 이벤트 구현
- 댓글 클릭시 get 이벤트 구현
#### 댓글 페이징 처리
- ReplyMapper.xml sql 부분 추가
- ReplyMapper Interface getCountByBno 추가(게시글에서 댓글 총 숫자 파악)
- ReplyPageDTO 추가(댓글수와 List<ReplyVO>)
- service 부분 reply 관련 추가
- contorller 부분 수정
- 페이징 관련 함수 추가
#### tb1_board 테이블 변경
- alter table tb1_board add (replycnt number default 0);
- update tb1_board set replycnt = (select count(rno) from tb1_reply where tb1_reply.bno = tb1_board.bno);
#### 댓글과 댓글 수에 대한 처리
- BoardVO, BoardMapper 수정(replyCnt)
- list.jsp(게시글 옆에 게시글의 댓글 수 표시) 수정

### BBS_6
#### 파일 첨부 관련 설정 추가
- pom.xml, web.xml, servlet-context.xml 관련 내용 추가
#### 파일 관련 테이블 추가
- tb1_attach table 
#### upload test 페이지 구현
- uploadAjax.jsp 추가 및 구현
#### uploadController 추가
- 파일 추가시에 해당 날짜의 폴더 자동 생성
- 이미지 파일 추가시 thumbnail 파일 생성 및 웹페이지 화면에 보이게 설정
- 추가 된 파일 삭제시에 원본 파일, 이미지의 경우 섬네일 파일까지 삭제 처리 구현
- 헤더의 User-Agent 기능을 이용해 브라우저 식별 후 각 브라우저에 맞게 인코딩 처리하여 한글 깨짐 방지 처리

### BBS_7
#### get.jsp, modify.jsp, register.jsp 수정
- 첨부파일 관련 항목 추가
- 파일 추가, 제거, 다운로드, 이미지의 경우 확대 기능
#### Quartz 라이브러리 추가
- quartz 2.3.0
- quartz-jobs 2.3.0
#### FileCheckTask.java 추가
- Quartz 라이브러리를 사용한 스케줄러로써 DB와 실제 저장소를 비교해 불필요한 파일 자동 삭제


### 기타
maven 관련 https://mvnrepository.com/

부트 스트랩 다운 https://startbootstrap.com/theme/sb-admin-2
