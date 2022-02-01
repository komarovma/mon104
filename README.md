<h1 class="code-line" data-line-start=0 data-line-end=1 ><a id="____1004_ELK_0"></a>Домашнее задание к занятию “10.04. ELK”</h1>
<h2 class="code-line" data-line-start=2 data-line-end=3 ><a id="__2"></a>Обязательные задания</h2>
<p class="has-line-data" data-line-start="4" data-line-end="6">Работаю на Windows 10 PRO +Dcoker Dekstop с итеграцией в WSL 2.0 (Ubuntu)<br>
Пришлось доработать для Docker для работы в WSL2 (лежит в репозитории)</p>
<p class="has-line-data" data-line-start="7" data-line-end="8">Также выполняем внутри Ubuntu (WSL) разрешения для файла конфигурации</p>
<pre><code>sudo chown root filebeat.yml
sudo chmod go-w filebeat.yml
</code></pre>
<p class="has-line-data" data-line-start="12" data-line-end="13">В профиле Windows пользователя</p>
<pre><code>wsl -d docker-desktop
sysctl -w vm.max_map_count=262144
</code></pre>
<p class="has-line-data" data-line-start="17" data-line-end="19">В связи с проблемным доступом к логам docker в WSL переписал на логи из /var/log/*.log<br>
(<a href="https://stackoverflow.com/questions/41219225/docker-log-driver-json-file-location-for-docker-for-windows">https://stackoverflow.com/questions/41219225/docker-log-driver-json-file-location-for-docker-for-windows</a>)</p>
<p class="has-line-data" data-line-start="20" data-line-end="21">В части filebeat.yml</p>
<pre><code>#  - type: container
  - type: log
    enabled: true
    paths:
#     - '/var/lib/docker/containers/*/*.log'
      - /var/log/*.log
    json.keys_under_root: true
#    document_type: docker
</code></pre>
<p class="has-line-data" data-line-start="31" data-line-end="32">В части logstash.conf</p>
<pre><code>input {
  tcp {
    port =&gt; 5046
    codec =&gt; plain
  }
}

filter {
 #    json {
        # source =&gt;  &quot;message&quot;  
    }
</code></pre>
<p class="has-line-data" data-line-start="46" data-line-end="47">В итоге, работающие контейнеры</p>
<pre><code>mike@HOMEDX79SR:~/mon104/help$ docker ps
CONTAINER ID   IMAGE                                                  COMMAND                  CREATED          STATUS          PORTS                                        NAMES
e0a5b3b1b5c5   docker.elastic.co/beats/filebeat:7.2.0                 &quot;/usr/local/bin/dock…&quot;   18 minutes ago   Up 18 minutes                                                filebeat
d8dd0882bf16   docker.elastic.co/logstash/logstash:6.3.2              &quot;/usr/local/bin/dock…&quot;   18 minutes ago   Up 18 minutes   5044/tcp, 9600/tcp, 0.0.0.0:5046-&gt;5046/tcp   logstash
819d0258f023   docker.elastic.co/kibana/kibana:7.11.0                 &quot;/bin/tini -- /usr/l…&quot;   18 minutes ago   Up 18 minutes   0.0.0.0:5601-&gt;5601/tcp                       kibana
07efdf936a15   docker.elastic.co/elasticsearch/elasticsearch:7.11.0   &quot;/bin/tini -- /usr/l…&quot;   18 minutes ago   Up 18 minutes   0.0.0.0:9200-&gt;9200/tcp, 9300/tcp             es-hot
5d457c90292b   python:3.9-alpine                                      &quot;python3 /opt/run.py&quot;    18 minutes ago   Up 18 minutes                                                some_app
5b0886101a7b   docker.elastic.co/elasticsearch/elasticsearch:7.11.0   &quot;/bin/tini -- /usr/l…&quot;   18 minutes ago   Up 18 minutes   9200/tcp, 9300/tcp                           es-warm
</code></pre>
<p class="has-line-data" data-line-start="58" data-line-end="62">Все скрины лежат в репозитории<br>
10_4_0.PNG скриншот скриншот интерфейса kibana.<br>
10_2_2.PNG скриншот  index-patterns.<br>
10_2_3.PNG cкриншот мониторинг.</p>