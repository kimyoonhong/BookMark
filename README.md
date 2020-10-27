# 즐겨찾기 

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
 
