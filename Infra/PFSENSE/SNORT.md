
```sh
alert tcp any any -> any any (msg:"Possible TCP Xmas Scan"; flags:SF; detection_filter:track by_src, count 100, seconds 60; sid:1000015;)
```