#### test with docker
##### example

```
docker run -p 5014:5014 --rm -it -v $(pwd)/pipeline/:/usr/share/logstash/pipeline/ docker.elastic.co/logstash/logstash-oss:6.5.1
```
##### Send test message to 11.aws.vpcflowlogs-filter.conf
```
echo '1530795694000 2 999999999999 eni-c21b65bf 172.31.24.25 37.157.4.23 4294 443 6 12 1300 1530785684 1530785864 ACCEPT OK' > /dev/tcp/127.0.0.1/5014
```

##### output for 11.aws.vpcflowlogs-filter.conf
```
{
            "host" => "172.17.0.1",
         "dstport" => 443,
      "log-status" => "OK",
         "dstaddr" => "37.157.4.23",
             "end" => 1530785864,
           "start" => 1530785684,
        "@version" => "1",
    "interface-id" => "eni-c21b65bf",
      "account-id" => "999999999999",
         "srcport" => 4294,
            "type" => "syslog",
         "version" => 2,
            "port" => 54764,
         "packets" => 12,
        "src_time" => 2018-07-05T13:01:34.000Z,
            "tags" => [
        [0] "geoip"
    ],
          "action" => "ACCEPT",
           "bytes" => 1300,
        "protocol" => 6,
         "srcaddr" => "172.31.24.25",
          "dst_ip" => {
              "latitude" => 55.7123,
          "country_name" => "Denmark",
             "longitude" => 12.0564,
                    "ip" => "37.157.4.23",
        "continent_code" => "EU",
              "location" => {
            "lon" => 12.0564,
            "lat" => 55.7123
        },
         "country_code2" => "DK",
              "timezone" => "Europe/Copenhagen",
         "country_code3" => "DK"
    },
      "@timestamp" => 2018-12-04T22:55:00.362Z
}
```


##### Send test message to 10.juniper-filter:
```
echo '<14>Dec 03 14:59:42 XXXXXXX-1 RT_FLOW: RT_FLOW_SESSION_CREATE: session created 10.0.0.1/48453->8.8.8.8/53 junos-dns-udp 10.0.0.1/48453->8.8.8.8/53 None None 17 my_vlan3 trust untrust 57651 N/A(N/A) reth5.0 UNKNOWN UNKNOWN UNKNOWN' >/dev/tcp/127.0.0.1/5014
```

##### Example of juniper-filter output
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

##### 12.aws_lambda-filter.conf
```
echo 'REPORT RequestId: a069ecd2-97a8-408f-b9f9-de3d1af6810a\tDuration: 337.06 ms\tBilled Duration: 400 ms \tMemory Size: 128 MB\tMax Memory Used: 73 MB\t\' > /dev/tcp/127.0.0.1/5014
```
```
{
          "host" => "172.17.0.1",
          "tags" => [
        [0] "lambda_filtered"
    ],
          "port" => 34778,
          "type" => "syslog",
      "@version" => "1",
        "lambda" => {
            "memory_used" => 73,
             "request_id" => "a069ecd2-97a8-408f-b9f9-de3d1af6810a",
        "billed_duration" => 400,
            "memory_size" => 128,
               "duration" => 337
    },
    "@timestamp" => 2019-01-30T22:14:02.184Z
}
```


##### debug inside container
```
docker exec -it $(docker ps | awk '/oss/ {print $1}') /bin/bash
```
