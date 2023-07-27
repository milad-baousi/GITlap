**First, create a directory storing GitLab configurations.**

```
mkdir gitlab
```
**Next, export the directory path using the following command.**

```
export GITLAB_HOME=$(pwd)/gitlab
```

**Next, navigate to the GitLab directory and create a docker-compose.yml file.**

```
cd gitlab
nano docker-compose.yml
```
**Add the following configurations:**

```
version: '3.7'
services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'localhost'
    container_name: gitlab-ce
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost'
    ports:
      - '8080:80'
      - '8443:443'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    networks:
      - gitlab
  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    container_name: gitlab-runner    
    restart: always
    depends_on:
      - web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - '$GITLAB_HOME/gitlab-runner:/etc/gitlab-runner'
    networks:
      - gitlab

networks:
  gitlab:
    name: gitlab-network
```

Save and close the file when you are done.

**Step 5 – Launch GitLab Container**
At this point, the docker-compose.yml file is ready to start the GitLab container. Run the following command inside the GitLab directory to launch the GitLab container.

```
docker-compose up -d
```

**You should see the following output.**
```
[+] Running 13/13
 ✔ gitlab-runner 3 layers [⣿⣿⣿]      0B/0B      Pulled                                                                                                            15.9s 
   ✔ 9621f1afde84 Pull complete                                                                                                                                    2.1s 
   ✔ d98190f30ae9 Pull complete                                                                                                                                   14.6s 
   ✔ d147c4edb07a Pull complete                                                                                                                                   14.8s 
 ✔ web 8 layers [⣿⣿⣿⣿⣿⣿⣿⣿]      0B/0B      Pulled                                                                                                                152.1s 
   ✔ 5544ebdc0c7b Pull complete                                                                                                                                    4.5s 
   ✔ c5213c581bf5 Pull complete                                                                                                                                    8.2s 
   ✔ a58d99176887 Pull complete                                                                                                                                    8.5s 
   ✔ 7ae8a2c59be5 Pull complete                                                                                                                                    8.8s 
   ✔ 2017c98fba37 Pull complete                                                                                                                                    9.0s 
   ✔ 2f81550514d7 Pull complete                                                                                                                                    9.2s 
   ✔ b7e289348f9c Pull complete                                                                                                                                    9.4s 
   ✔ d5fd7dcd275c Pull complete                                                                                                                                  151.3s 
[+] Running 3/3
 ✔ Network gitlab-network   Created                                                                                                                                0.1s 
 ✔ Container gitlab-ce      Started                                                                                                                                0.8s 
 ✔ Container gitlab-runner  Started                                                                                                                                1.3s
```

**You can verify the GitLab container status using the following command.**

```
docker-compose ps
```

You should see the following output.
```
NAME                IMAGE                         COMMAND                  SERVICE             CREATED             STATUS                             PORTS
gitlab-ce           gitlab/gitlab-ce:latest       "/assets/wrapper"        web                 31 seconds ago      Up 30 seconds (health: starting)   22/tcp, 0.0.0.0:8080->80/tcp, :::8080->80/tcp, 0.0.0.0:8443->443/tcp, :::8443->443/tcp
gitlab-runner       gitlab/gitlab-runner:alpine   "/usr/bin/dumb-init …"   gitlab-runner       31 seconds ago      Up 29 seconds
```   
**You can also verify the GitLab listening ports using the following command.**
```
ss -antpl | grep docker
```
**You will get the following output.**
```
LISTEN 0      4096         0.0.0.0:8080       0.0.0.0:*    users:(("docker-proxy",pid=12635,fd=4))
LISTEN 0      4096         0.0.0.0:8443       0.0.0.0:*    users:(("docker-proxy",pid=12617,fd=4))
LISTEN 0      4096            [::]:8080          [::]:*    users:(("docker-proxy",pid=12641,fd=4))
LISTEN 0      4096            [::]:8443          [::]:*    users:(("docker-proxy",pid=12622,fd=4))
```
***Step 6 – Access GitLab Web UI**
At this point, GitLab is started and listening on port 8080. You can now access it using the URL http://your-server-ip:8080. You should see the GitLab login screen.

![image](https://github.com/milad-baousi/GITlap/assets/113288076/1f458bed-1fd0-4558-80c6-2f4d3e571ac5)

** Next, go back to your GitLab terminal interface and retrieve the GitLab password with the following command.**
```
docker exec -it gitlab-ce grep 'Password:' /etc/gitlab/initial_root_password
```
**You should see the GitLab password in the following output.**
```
Password: Kx1MoTQ80iKJkA3SXatepaFCOfsi/DkLe3MXEplfERU=
```
![image](https://github.com/milad-baousi/GITlap/assets/113288076/1f458bed-1fd0-4558-80c6-2f4d3e571ac5)
