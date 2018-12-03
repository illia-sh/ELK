#### test with docker
##### example

```
docker run -p 5014:5014 --rm -it -v $(pwd)/pipeline/:/usr/share/logstash/pipeline/ docker.elastic.co/logstash/logstash-oss:6.5.1
```

Send test message for 10.juniper-filter:
```
echo '<14>Dec 03 14:59:42 XXXXXXX-1 RT_FLOW: RT_FLOW_SESSION_CREATE: session created 10.0.0.1/48453->8.8.8.8/53 junos-dns-udp 10.0.0.1/48453->8.8.8.8/53 None None 17 my_vlan3 trust untrust 57651 N/A(N/A) reth5.0 UNKNOWN UNKNOWN UNKNOWN' >/dev/tcp/127.0.0.1/5014
```

Example of juniper-filter output
```
{
    "syslog_program" => "RT_FLOW",
       "received_at" => "2018-12-03T21:42:16.652Z",
        "syslog_pri" => "14",
      "src_nat_rule" => "junos-dns-udp",
        "src_nat_ip" => "10.0.0.1",
     "received_from" => "172.17.0.1",
        "@timestamp" => 2018-12-03T14:59:42.000Z,
       "policy_name" => "my_vlan3",
        "session_id" => "57651",
        "dst_nat_ip" => "8.8.8.8",
              "tags" => [
        [0] "RT_FLOW_SESSION",
        [1] "geoip"
    ],
         "interface" => "reth5.0",
          "dst_zone" => "untrust",
              "host" => "172.17.0.1",
      "@source_host" => "XXXXXXX-1",
            "src_ip" => "10.0.0.1",
          "src_zone" => "trust",
          "src_port" => "48453",
          "dst_port" => "53",
              "type" => "syslog",
              "port" => 54670,
               "msg" => "RT_FLOW_SESSION_CREATE: session created 10.0.0.1/48453->8.8.8.8/53 junos-dns-udp 10.0.0.1/48453->8.8.8.8/53 None None 17 my_vlan3 trust untrust 57651 N/A(N/A) reth5.0 UNKNOWN UNKNOWN UNKNOWN",
      "dst_nat_port" => "53",
             "geoip" => {
          "country_name" => "United States",
                    "ip" => "8.8.8.8",
         "country_code3" => "US",
         "country_code2" => "US",
              "location" => {
            "lat" => 37.751,
            "lon" => -97.822
        },
             "longitude" => -97.822,
              "latitude" => 37.751,
        "continent_code" => "NA"
    },
      "src_nat_port" => "48453",
            "dst_ip" => "8.8.8.8",
             "event" => "<14>Dec 03 14:59:42 XXXXXXX-1 RT_FLOW: RT_FLOW_SESSION_CREATE",
       "protocol_id" => "17",
          "@version" => "1"
}
```

##### debug inside container
```
docker exec -it $(docker ps | awk '/oss/ {print $1}') /bin/bash
```
