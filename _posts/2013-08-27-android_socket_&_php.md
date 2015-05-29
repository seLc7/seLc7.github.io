---
title: 'Android socket & php相关'
layout: post
tags:
    - tech
    - android
    - java
---

使用wamp时，从wampserver里的php下的php扩展修改打开php_sockets。因为wamp里有两个php.ini，一个在php目录下，一个在apache的bin目录里，手动修改两个都要修改。

**socket**

1. java：
    
    ```java
    ServerSocket serverSocket=new ServerSocket(8888);  
            while(true){  
                Socket socket=serverSocket.accept();  
                BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));  
                String line = br.readLine();  
                System.out.println(line);  
                br.close();  
                socket.close();  
                OutputStream out=socket.getOutputStream();  
                out.write("hahaha,try?OK?".getBytes("utf-8"));  
                out.close();  
                socket.close();  
            }  
    ```

    `Socket socket = new Socket("192.168.0.53", 10007);      //客户端 `

2.  php：

    ```php
    <?php  

    error_reporting(E_ALL);
    // 建立server端socket  
    $tcp = getprotobyname("tcp");  
    $socket = socket_create(AF_INET, SOCK_STREAM, $tcp); 
    if(($sock=socket_create(AF_INET,SOCK_STREAM,SOL_TCP))<0)
    {
      echo "socket_create() 失败的原因是:".socket_strerror($sock)."<br>";
    } 
    socket_bind($socket, '192.168.0.53', 10007);       //绑定要监听的端口  
    socket_listen($socket);       //监听端口  

    //初始化一个数据，和客户端通信  
    $buffer = "connect";  
    while (true) {  
        // 接受一个socket连接  
        $connection = socket_accept($socket);  
        if(!$connection){  
            echo "connect fail";  
        }else{  
            echo "Socket connected\n";  
                // 向客户端传递一个信息数据  
            if ($buffer != "") {  
                echo "send data to client\n";  
                socket_write($connection, $buffer . "\n");  
                echo "Wrote to socket\n";  
            } else {  
                echo "no data in the buffer\n" ;  
            }  
                // 从客户端取得信息  
            while ($data = socket_read($connection, 1024)) { 
                echo ($data); 
                printf("Buffer: " . $data . "\n");  
                        //取得信息给客户端一个反馈  
                //socket_write($connection, "Information Received\n");  
            }  
        }  

        socket_close($connection);  
        //关闭 socket  
        printf("Closed the socket\n");  
    }  
    ?>  
    
    ```
