DataLocker는 개인 사용자를 위한 데이터, 파일 암호화 프로그램입니다.
동작을 위해서는 사용자를 위한 DB 서버를 리눅스 mysql을 활용해 구축하여야 합니다.
DataLocker.pdf에서 추진배경 및 목적, 그외 자세한 내용을 확인하실 수 있습니다.

DB의 구성은 암호화키가 저장될 테이블을
```sql
CREATE TABLE 키저장용테이블 (
    fileID INT AUTO_INCREMENT PRIMARY KEY,
    filepath VARCHAR(255) NOT NULL,
    EKey VARCHAR(255) NOT NULL
);
```
위의 예시처럼 구성하시고,
```sql
CREATE TABLE 로그용테이블 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    filepath VARCHAR(255) NOT NULL,
    operation VARCHAR(255) NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
의 테이블을 구성하여 동작에 대한 로그를 저장하도록 합니다.

DB의 사용자는 계정이 2개 필요한데 암,복호화 기능을 이용하기 위한 계정을 
아래의 예시와 같이 만든 뒤 권한을 부여하시고
```sql
CREATE USER '계정명'@'접속할IP' IDENTIFIED BY '비밀번호';
```
```sql
GRANT ALL PRIVILEGES ON 사용할DB명.키저장용테이블 TO '계정명'@'접속할IP';
```
웹을 통해 로그를 확인하기 위한 관리자 계정을
아래의 예시와 같이 만들어주세요.
```sql
CREATE USER '관리자계정명'@'%' IDENTIFIED BY '사용할비밀번호를 sha256해싱한 값';
```
```sql
GRANT SELECT ON 사용할DB명.로그용테이블 TO '관리자계정명'@'%';
```

DB 서버에 로그 관리 웹 서비스를 이용할 수 있도록 apache2를 설정해주시고 
외부에서 접근할 수 있게 DLform.html과 check.php를 apache2의 설정된 경로 디렉터리에 위치시켜줍니다.

암,복호화는 세팅 완료 후 DataLocker_ver1.0.py를 실행하여 DB 서버에 접속하면 원하는 경로를 입력하여 실행 가능합니다.
로그는 구축한 서버의 IP/DLform.html로 접속하고, 관리자계정으로 로그인하여 확인할 수 있습니다.

보다 자세한 내용은 DataLocker.pdf를 참고해주세요.
