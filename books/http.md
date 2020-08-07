# HTTP 완벽 가이드

- Start reading: 2020-04-13
- End reading: 2020-05-05


### 읽기 전

여기저기에서 많이 추천을 받은 책이다. 그림으로 배우는 HTTP NETWORK BASIC을 읽기는 했는데 뭔가 겉핥기으 느낌을 지우지 못하였는데, 이 책에 도전해보기로 하였다.

조금 걱저은 10장 이내로 구성된 이전 책과 달리 해당 책은 20장이 넘고, 600여페이지에 달하는 책이기 때문에 읽다가 지치지 않을까 하는 걱정이 조금 들지만..

읽다가 넘어가고 싶은 부분은 과감히 넘기고, 다른 책으로 넘어가고 싶다면 다른 책도 병행해서 읽으면서 부담 없이 한 장 씩 읽어보아야겠다. (한 장이 길 경우에는 하루에 반 장씩이라도 읽는 것을 목표로..)

그럼 시작! (2020-04-13)


## 1장

telnet을 이용해서 직접 http 보내보기

맥 OS X에서 high siera 이후에는 telnet이 기본으로 설치되어 있지 않는다. 설치해주자

```
brew install telnet
```

책 예시의 `http://www.joes-hardware.com/`은 만료되어 `https://www.joeshardwareonline.com/`로 접속하면 된다.


## 5장.

### perl로 기초적인 웹 서버 만들기

`type-o-serve.pl` 파일을 만들고 다음 내용을 입력하자.

```
#!/usr/bin/perl

use Socket;
use Carp;
use FileHandle;

# 1. use 8080 port by default
$port = (@ARGV ? $ARGV[0] : 8080);

# 2. Create local TCP socket and listen connections
$proto = getprotobyname('tcp');
socket(S, PF_INET, SOCK_STREAM, pack("l", 1)) || die;
bind(S, sockaddr_in($port, INADDR_ANY)) || die;
listen(S, SOMAXCONN) || die;

# 3. Print start message
printf("    <<<Type-O-Server Accepting on Port %d>>>\n\n",$port);

while (1)
{
    # 4. Waiting connection C
    $cport_caddr = accept(C, S);
    ($cport,$caddr) = sockaddr_in($cport_caddr);
    C->autoflush(1);

    # 5. 누구로부터 커넥션인지 출력
    $cname = gethostbyaddr($caddr,AF_INET);
    printf("    <<<Request From '%s'>>>\n",$cname);

    # 6. 빈 줄이 나올 때까지 요청 메시지를 읽어서 화면에 출력
    while ($line = <C>)
    {
        print $line;
        if ($line =~ /^\r/) { last; }
    }

    # 7. 응답 메세지를 위한 프롬프트를 만들고 응답줄을 입력
    # "." 하나만으로 되어 있는 줄이 입력되기 전까지, 입력된 줄을 클라이언트에게 보낸다.
    printf("    <<<Type Response Followed by '.'>>>\n");

    while ($line = <STDIN>)
    {
        $line =~ s/\r//;
        $line =~ s/\n//;
        if ($line =~ /^\./) { last; }
        print C $line . "\r\n";
    }
    close()
}
```

이제 해당 파일을 실행시켜 간이 웹서버로 작동시킬 수 있다.


```
$ perl type-o-serve.pl 8080
```

이제 브라우저나 cli에서 `localhost:8080`으로 요청을 보내면 해당 프로그램에 요청이 뜨고 응답을 보낼 수 있다.


```
    <<<Type-O-Server Accepting on Port 8080>>>

    <<<Request From 'localhost'>>>
GET / HTTP/1.1
Host: localhost:8080
User-Agent: curl/7.65.2
Accept: */*

    <<<Type Response Followed by '.'>>>
# 여기서부터 입력한 응답
HTTP/1.0 200 OK
Connection: close
Content-type: text-plain

Hi there!
.
```


