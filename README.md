1.1 - Kubernetes Kurulumu (Ref:1)
1.2 - Server 0 da sadece pod çalışacak şekilde tanımlamak için (Ref:2)
    >> kubectl taint nodes server-sahin-besinci-0 dedicated=true:NoSchedule
    >> kubectl edit node server-sahin-besinci-0
        …  server-sahin-besinci-0-affinity: "true"
      Tanımları yapılarak .yaml dosyası düzenlenir.
      
      spec:
      ..
        tolerations:
          - key: "dedicated"
            operator: "Equal"
            value: "true"
            effect: "NoSchedule"
        affinity:
          nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
              - matchExpressions:
                - key: server-sahin-besinci-0-affinity
                  operator: In
                  values:
                  - "true"

1.3 ve 1.4 - ingress Prometheus Kurulumu (Ref:3)
      http://8.208.93.32:30900/ (internal) - external durumda açılmıştır. Testlerin yapıldıktan sonra internal olacak şekilde erişimi kapatılabilir.
      

2. - Consul Kurulumu: (Ref:4)
      Native olarak Server 2 de kurulmuştur.
      Consul UI: http://8.208.92.21:8500/ui/us-central/services

      >> sudo vi /etc/consul.d/config.json
      {
          "bootstrap_expect": 1,
          ….
          "encrypt": "xxxx/xxxx/xxxx/xxxx/t/xxxx=",
          ….
          "start_join": [
              "192.168.0.86"
          ],
          ….
      }
  
3. -  Federation Prometheus Kurulumu (Ref:5)
      >> tar xvfz prometheus-*.tar.gz
      >> cd prometheus-*

3.1 - Prometheus register edilir (Ref:6)
      >> consul services register consule-register-prometheus-service.json
      >> consul catalog services
      Consul UI: http://8.208.92.21:8500/ui/us-central/services
    
4. - Grafana Kurulumu ve External Prometheus datasource eklenmesi (Ref:7)
    >> service grafana-server start
    >> service grafana-server status
    >> /sbin/chkconfig --add grafana-server

  
Referanslar:
1: https://enterprisecoding.com/centos-7-uzerine-kubernetes-kurulum/
2: https://banzaicloud.com/blog/k8s-taints-tolerations-affinities/
3: https://github.com/sahinbesinci/case-sahin-besinci.abc/tree/master/prometheus
4: https://devopscube.com/setup-consul-cluster-guide/
5: https://devopscube.com/install-configure-prometheus-linux/
6: https://yetiops.net/posts/prometheus-consul-node_exporter/
7: https://grafana.com/grafana/download
