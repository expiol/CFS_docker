version: '3'

services:
  struts2:
    image: vulhub/struts2:2.3.30
    container_name: "struts2"
    environment:
      - FLAG=${flag1}
    restart: always
    networks:
      internal_net:
        ipv4_address: 172.16.238.10  # 仅在内部网络可访问

  tomcat:
    image: vulhub/tomcat:9.0.30
    ports:
      - "8002:8080"  # 仅暴露 Tomcat 的 8080 端口给外网，通过此服务进行攻击
    environment:
      - FLAG=${flag2}
    restart: always
    networks:
      internal_net:
        ipv4_address: 172.16.238.81
      external_net:
        ipv4_address: 10.10.5.6  # 外部网络中对攻击者可见

  solr:
    image: vulhub/solr:7.0.1
    container_name: "solr-CVE-2017-12629"
    environment:
      - FLAG=${flag3}
    restart: always
    networks:
      internal_net:
        ipv4_address: 172.16.238.88  # 仅在内部网络可访问
      restricted_net:
        ipv4_address: 172.42.66.10

  jenkins:
    image: vulhub/jenkins:2.138
    container_name: "jenkins-CVE-2018-1000861"
    environment:
      - FLAG=${flag4}
    restart: always
    networks:
      restricted_net:
        ipv4_address: 172.42.66.77  # 仅在受限网络中可访问

networks:
  internal_net:
    ipam:
      config:
        - subnet: "172.16.0.0/16"
  external_net:
    ipam:
      config:
        - subnet: "10.10.5.0/24"
  restricted_net:
    ipam:
      config:
        - subnet: "172.42.66.0/24"
