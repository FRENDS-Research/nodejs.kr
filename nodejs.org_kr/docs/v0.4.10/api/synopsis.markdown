## 개요(Synopsis)

An example of a [web server](http.html) written with Node which responds with 'Hello
World':

Node로 작성된 'Hello World'를 응답하는 [웹 서버](http.html)의 예제:

    var http = require('http');

    http.createServer(function (request, response) {
      response.writeHead(200, {'Content-Type': 'text/plain'});
      response.end('Hello World\n');
    }).listen(8124);

    console.log('Server running at http://127.0.0.1:8124/');

To run the server, put the code into a file called `example.js` and execute
it with the node program

서버를 실행시키기 위해서 `example.js` 파일에 코드를 저장하고 node 프로그램으로 실행시킨다.

    > node example.js
    Server running at http://127.0.0.1:8124/

All of the examples in the documentation can be run similarly.

문서상의 모든 예제들은 비슷하게 실행할 수 있다.
