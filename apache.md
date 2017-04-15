# Apache Short Codes

### ab - Apache HTTP server benchmarking tool
```shell
ab -n 100000 -c 50 https://example.com/myapp
```
* -n : Number of requests to perform for the benchmarking session. The default is to just perform a single request which usually leads to non-representative benchmarking results.
* -c : Number of multiple requests to perform at a time. Default is one request at a time.
* url : [http[s]://]hostname[:port]/path


#### Reference [Apache](https://httpd.apache.org/docs/2.4/programs/ab.html)
