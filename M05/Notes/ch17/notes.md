DEV220 | M05 NOTES

## Chapter 17. Data in Space: Networks
Sockets

```python
# udp_server.py
from datetime import datetime
import socket

server_address = ('localhost', 6789)
max_size = 4096

print('Starting the server at', datetime.now())
print('Waiting for a client to call.')
server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server.bind(server_address)

data, client = server.recvfrom(max_size)

print('At', datetime.now(), client, 'said', data)
server.sendto(b'Are you talking to me?', client)
server.close()
```

```python
# udp_client.py
import socket
from datetime import datetime

server_address = ('localhost', 6789)
max_size = 4096

print('Starting the client at', datetime.now())
client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
client.sendto(b'Hey!', server_address)
data, server = client.recvfrom(max_size)
print('At', datetime.now(), server, 'said', data)
client.close()
```

```cmd
python udp_server.py
```

```cmd
python udp_client.py
```

```python
# tcp_client.py
import socket
from datetime import datetime

address = ('localhost', 6789)
max_size = 1000

print('Starting the client at', datetime.now())
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(address)
client.sendall(b'Hey!')
data = client.recv(max_size)
print('At', datetime.now(), 'someone replied', data)
client.close()
```

```python
# tcp_server.py
from datetime import datetime
import socket

address = ('localhost', 6789)
max_size = 1000

print('Starting the server at', datetime.now())
print('Waiting for a client to call.')
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(address)
server.listen(5)

client, addr = server.accept()
data = client.recv(max_size)

print('At', datetime.now(), client, 'said', data)
client.sendall(b'Are you talking to me?')
client.close()
server.close()
```

```cmd
$ python tcp_client.py
```

## Python ZeroMQ library

```cmd
pip install pyzmq
```

```python
# zmq_server.py
import zmq

host = '127.0.0.1'
port = 6789
context = zmq.Context()
server = context.socket(zmq.REP)
server.bind("tcp://%s:%s" % (host, port))
while True:
    #  Wait for next request from client
    request_bytes = server.recv()
    request_str = request_bytes.decode('utf-8')
    print("That voice in my head says: %s" % request_str)
    reply_str = "Stop saying: %s" % request_str
    reply_bytes = bytes(reply_str, 'utf-8')
    server.send(reply_bytes)
```

```python
# zmq_client.py
import zmq

host = '127.0.0.1'
port = 6789
context = zmq.Context()
client = context.socket(zmq.REQ)
client.connect("tcp://%s:%s" % (host, port))
for num in range(1, 6):
    request_str = "message #%s" % num
    request_bytes = request_str.encode('utf-8')
    client.send(request_bytes)
    reply_bytes = client.recv()
    reply_str = reply_bytes.decode('utf-8')
    print("Sent %s, received %s" % (request_str, reply_str))
```

```cmd
python zmq_server.py &
```

```cmd
python zmq_client.py
```

```cmd
python zmq_server.py
```

## Docker

```cmd
$ pip install docker
```
Kubernetes

```cmd
$ pip install kubernetes
```


---
## Module 5 Lab - Case Study: Containers and CI/CD
The purpose of this assignment is to learn about Docker and CI technologies

## Prerequisite:

Create a GitLab account at https://about.gitlab.com/

You will watch the following videos and perform the activities:

[Docker and PythonLinks to an external site.](https://www.youtube.com/watch?v=jtBVppyfDbE&ab_channel=TheDigitalLife)

## Instructions

### Complete the following steps:

-   Navigate to the following link to get familiar with Gitlab and deployment -  [CONTINUOUS INTEGRATION WITH GITLAB CI AND DOCKERLinks to an external site.](https://codebabel.com/ci-gitlabci-docker/)
    -   ADDITIONAL RESOURCES:
        -   -   -   https://docs.gitlab.com/ee/ci/docker/using_docker_images.html
                -   [https://youtu.be/qP8kir2GUgo?si=0IPwIooTf70z5U5VLinks to an external site.](https://youtu.be/qP8kir2GUgo?si=0IPwIooTf70z5U5V)[![](https://ivylearn.ivytech.edu/images/play_overlay.png)](https://youtu.be/qP8kir2GUgo?si=0IPwIooTf70z5U5V)
                -   [https://spacelift.io/blog/docker-ci-cdLinks to an external site.](https://spacelift.io/blog/docker-ci-cd)
                -   [https://www.techworld-with-nana.com/post/gitlab-ci-cd-for-beginnersLinks to an external site.](https://www.techworld-with-nana.com/post/gitlab-ci-cd-for-beginners)
                -   [https://dev.to/arbythecoder/how-to-set-up-a-cicd-pipeline-with-gitlab-a-beginners-guide-46b9Links to an external site.](https://dev.to/arbythecoder/how-to-set-up-a-cicd-pipeline-with-gitlab-a-beginners-guide-46b9)
        
-   When you get to editing the gitlab-ci.yml you may need to replace this line:
    -   - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN [registry.gitlab.com Links to an external site.](http://registry.gitlab.com/)
        
    -   With this line
    -   docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY


▬▬▬  R E F E R E N C E S  🔗▬▬▬

►   Git repo to build the CI/CD pipeline for: https://gitlab.com/nanuchi/gitlab-cic...
►   Original forked python demo app: https://github.com/benc-uk/python-dem...
►   Docker in Docker Image: https://hub.docker.com/_/docker
►   Docker in Docker Gitlab Docs: https://docs.gitlab.com/ee/ci/docker/...
►   Install Python and Pip: https://www.python.org/downloads/