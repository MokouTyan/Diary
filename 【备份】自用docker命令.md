### jupyter

搬运要改路径

```
docker run --name jupyter -v /home/mokou/jupyter/work:/home/jovyan/work -d -p 7000:8888 jupyter/datascience-notebook start-notebook.sh — NotebookApp.token=’’
```

如果要自建可以看：[基於docker的伺服器端使用jupyter notebook的方法 - 程式人生 (796t.com)](https://www.796t.com/content/1550446021.html)

### 阿里云动态DNS

要修改前三个选项

```
docker run -d --restart=always --net=host \
    -e "AKID=修改" \
    -e "AKSCT=修改" \
    -e "DOMAIN=修改" \
    -e "REDO=300" \
    -e "TTL=600" \
    -e "TIMEZONE=8.0" \
    -e "TYPE=A" \
    sanjusss/aliyun-ddns

```

### TeamSpeak语音服务器

```
docker run -p 9987:9987/udp -p 10011:10011 -p 10022:10022 -p 10080:10080 -p 10443:10443 -p 30033:30033 -p 41144:41144 -e TS3SERVER_LICENSE=accept -d teamspeak
```

### portainer

```
docker run -d -p 9000:9000 \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name prtainer \
    portainer/portainer
```

### netdata

```
docker run -d --name=netdata \
  -p 19999:19999 \
  -v netdataconfig:/etc/netdata \
  -v netdatalib:/var/lib/netdata \
  -v netdatacache:/var/cache/netdata \
  -v /etc/passwd:/host/etc/passwd:ro \
  -v /etc/group:/host/etc/group:ro \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /etc/os-release:/host/etc/os-release:ro \
  --restart unless-stopped \
  --cap-add SYS_PTRACE \
  --security-opt apparmor=unconfined \
  netdata/netdata
```

### 下载器

需要修改路径

```
docker run -d \
    --name aria2-pro \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -e PUID=$UID \
    -e PGID=$GID \
    -e RPC_SECRET=raspberry \
    -e RPC_PORT=6800 \
    -p 6800:6800 \
    -e LISTEN_PORT=6888 \
    -p 6888:6888 \
    -p 6888:6888/udp \
    -v /home/pi/aria/config:/config \
    -v /home/pi/aria/downloads:/downloads \
    p3terx/aria2-pro

docker run -d \
    --name ariang \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -p 6880:6880 \
    p3terx/ariang
```

### jellyfin

需要修改路径

```
docker run -d \
  --name jellyfin \
  --restart unless-stopped \
  -v /home/pi/jellyfin/config:/config \
  -v /home/pi/jellyfin/cache:/cache \
  -v /home/pi/jellyfin/video:/video \
  -p 8096:8096 \
  -p 8920:8920 \
  jellyfin/jellyfin
```

