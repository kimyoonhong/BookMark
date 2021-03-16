# 리눅스 Maria 소스 컴파일 설치 

* CentOS 7 MariaDB 10.1.47 소스 컴파일 설치

* 컴파일을 위한 패키지 다운로드
yum install cmake ncurses ncurses-devel bison gcc gcc-c++ gnutls-devel 
yum -y install gcc gcc-c++ libtermcap-devel gdbm-devel zlib* libxml* freetype* libpng* libjpeg* iconv flex gmp ncurses-devel cmake.x86_64 libaio gnutls*

wget https://downloads.mariadb.com/MariaDB/mariadb-10.3/bintar-linux-x86_64/mariadb-10.3.25-linux-x86_64.tar.gz
wget https://downloads.mariadb.org/interstitial/mariadb-10.4.15/bintar-linux-x86_64/mariadb-10.4.15-linux-x86_64.tar.gz/from/https%3A//ftp.harukasan.org/mariadb/

* 소스파일의 압축 해제
tar xvfz mariadb-10.1.47.tar.gz 

* 빌드 디렉토리 생성
mkdir build-mariadb
cd build-mariadb

* 유저, 그룹 생성
(그룹생성) groupadd mysql
(유저생성) useradd -g mysql mysql
(내가 한 방식) user add -g 500 mysql
(내가 한 방식) gpasswd -a mysql wheel (wheel 그룹에 추가, 원격 접속 허용 그룹)

* 컴파일 옵션 확인 ( 사용 X)
cmake ../mariadb-10.1.47 -LH

* 옵션 변경 ( -D 를 붙여서 값 입력 )
cmake -DWITH_READLINE=1 -DWITH_READLINE=1 -DWITH_SSL=bundled -DWITH_ZLIB=system -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DENABLED_LOCAL_INFILE=1 -DWITH_EXTRA_CHARSETS=all -DWITH_ARIA_STORAGE_ENGINE=1 -DWITH_XTRADB_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_FEDERATEDX_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DINSTALL_SYSCONFDIR=/maria/mariadb-10.1.47/etc -DINSTALL_SYSCONF2DIR=/maria/mariadb-10.1.47/etc/my.cnf.d -DMYSQL_TCP_PORT=3307 -DCMAKE_INSTALL_PREFIX=/maria/mariadb-10.1.47 -DMYSQL_DATADIR=/maria/mariadb-10.1.47/data -DMYSQL_UNIX_ADDR=/maria/mariadb-10.1.47/socket/mysql.socket
 
   ( Tip 컴파일 실패시 CmakeCache.txt 삭제,   rm -rf CmakeCashe.txt  )

* make && make install
 ( 컴파일 결과로 실제 파일 쓰기 )

* 파일 소유권, 파일권한 설정 
chown mysql:postgres -R /maria/mariadb-10.1.47
chmod 755 /maria/mariadb-10.1.47 -R
cd /maria/mariadb-10.1.47/scripts

* DB생성
./mysql_install_db --user=mysql --basedir=/maria/mariadb-10.1.47 --datadir=/maria/mariadb-10.1.47/data

!!!!!!!!!!!! 여기서 user를 mysql이 아닌 maria 로 하면 오류 !!!!!!!!

* DB생성 후 다시 권한 및 유저 설정
chown mysql:postgres -R /maria/mariadb-10.1.47
chmod 755 /maria/mariadb-10.1.47 -R

* cnf 파일 이동
cp /maria/mariadb-10.1.47/support-files/my-huge.cnf /maria/mariadb-10.1.47/etc/my.cnf

* 실행 테스트
cd /maria/mariadb-10.1.47/support-files
./mysql.server start

* root 비밀번호 설정
cd /maria/mariadb-10.1.47/bin
./mysqladmin -u root password '비밀번호'

* DB 접속 확인
cd /maria/mariadb-10.1.47/bin
./mysql -u root -p
(비밀번호 입력 창) 

* 심볼릭 링크 생성
ln -s /maria/mariadb-10.1.47/bin/mysql /usr/bin/mariadb
ln -s /maria/mariadb-10.1.47/bin/mysqldump /usr/bin/mariadbdump 

* systemctl 서비스 등록 ( root 계정으로 해야함 )
vi /etc/systemd/system/mariadb.service ( 해당 파일 생성 )

[Unit]
Description=mariadb-10.1.47
After=syslog.target network.target

[Service]
Type=forking
User=mysql
Group=postgres
ExecStart=/maria/mariadb-10.1.47/support-files/mysql.server start
ExecStop=/maria/mariadb-10.1.47/support-files/mysql.server stop

[Install]
WantedBy=multi-user.target

:wq 
( 종료 )

systemctl daemon-reload

systemctl enable mariadb.service

systemctl start mariadb.service

systemctl status mariadb.service

----------------------------------------------------------------------------------------------------------------------------------------------------------------------



10.4버전 링크 : https://server-talk.tistory.com/292

###리눅스 인터넷 안되는 곳에서 yum 레파지토리 설정
#### 네트워크가 되는 곳에서

- 필요한 패키지 파일을 yum으로 다운로드 후 sftp로 파일전송 

- yum 레파지토리 설정에 필요한 createrepo 설치

yum install -y --downloadonly --downloaddir=/root/repository createrepo

- 컴파일 패키지

yum -y install gcc gcc-c++ libtermcap-devel gdbm-devel zlib* libxml* freetype* libpng* libjpeg* iconv flex gmp ncurses-devel cmake.x86_64 libaio gnutls*

- 설치 순서 

yum -y install deltarpm-3.6-3.el7.x86_64.rpm 
yum -y install python-deltarpm-3.6-3.el7.x86_64.rpm 
yum -y install createrepo-0.9.9-28.el7.noarch.rpm

####  네트워크가 안 되는 곳에서

(만약 createrepo가 네트워크가 안되는곳에서 설치가 안되있을 시 되는곳에서 받은 후 sftp로 받는다)



vi /etc/yum.repos.d/local.repo

/etc/yum.repos.d/ 디렉토리에 임의의 파일을 만든다 (ex. local.repo)

[local-repo] 

name=local-repo 

baseurl=file:///disk  (/disk 경로에 필요한 패키지 파일을 넣어둔다)

gpgcheck=0 

enabled=1 

priority=1


 
 * yum 레파지토리 확인

yum reposlist
yum clean all
 
 
 관련 링크 :
https://medium.com/@zoo5252/%EC%9D%B8%ED%84%B0%EB%84%B7-%EC%97%86%EB%8A%94-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-%ED%8C%A8%ED%82%A4%EC%A7%80-%EC%84%A4%EC%B9%98-7ca0c36bc0f5





 

