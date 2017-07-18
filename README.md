修改apache配置文件
vim httpd.conf
ExtendedStatus On 
<location /server-status>
   SetHandler server-status
   Order allow,deny
   Allow from localhost 192.168.8.131 192.168.8.87 192.168.6.119
</location>

下载解压zapache-master.zip
wget https://codeload.github.com/1096308852/zapache/zip/master
unzip master
cd zapache-master
mkdir /usr/local/zabbix/scripts
cp zapache /usr/local/zabbix/scripts/
chmod +x /usr/local/zabbix/scripts/zapache
cp /usr/local/zabbix/etc/zabbix_agentd.conf /usr/local/zabbix/etc/zabbix_agentd.conf_20170718
echo "UserParameter=zapache[*],/usr/local/zabbix/scripts/zapache \$1" >> /usr/local/zabbix/etc/zabbix_agentd.conf


重启zabbix_agentd 
service zabbix_agentd restart

在zabbix_server上验证
/usr/local/zabbix/bin/zabbix_get -s 192.168.6.100 -p 10050 -k zapache[ping]


通过浏览器导入模板名字为zapache-template.xml  Template App Apache Web Server zapache


上面的zapache默认是监控80端口的 
如果想监控其他端口可以修改zapache文件的
STATUS_URL="http://localhost/server-status?auto" 为 STATUS_URL="http://localhost:5961/server-status?auto"  其中5961改为想要的端口

如果想一台主机同时监控多个httpd 如端口为5961，5962
cp zapache zapache_5961
cp zapache zapache_5961
修改zapache_5961中的STATUS_URL="http://localhost/server-status?auto" 为 STATUS_URL="http://localhost:5961/server-status?auto"  其中5961改为想要的端口
修改zapache_5962中的STATUS_URL="http://localhost/server-status?auto" 为 STATUS_URL="http://localhost:5962/server-status?auto"  其中5962改为想要的端口

echo "UserParameter=zapache_5961[*],/usr/local/zabbix/scripts/zapache_5961 \$1" >> /usr/local/zabbix/etc/zabbix_agentd.conf
echo "UserParameter=zapache_5962[*],/usr/local/zabbix/scripts/zapache_5962 \$1" >> /usr/local/zabbix/etc/zabbix_agentd.conf

zabbix模板可以全克隆出来一个。也可以用我改好的模板：Apache_5961.xml，Apache_5962.xml 可将文件内容的5961，5962改为想要的端口
全克隆出来的得改：模版名称，监控项中的键值，触发器名称，图形名称。
