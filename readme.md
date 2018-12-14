main.go
=======

    package main
     
    import (
        "fmt"
        "github.com/influxdata/influxdb/client/v2"
        "log"
        "time"
    )
     
    const (
        MyDB     = "test"
        username = "admin"
        password = ""
    )
     
    func main() {
        conn, err := client.NewHTTPClient(client.HTTPConfig{
            Addr:     "http://127.0.0.1:8086",
            Username: username,
            Password: password,
        })
        if err != nil {
            log.Fatal(err)
        }
        fmt.Println(conn)
        defer conn.Close()
        
        q := client.NewQuery("CREATE DATABASE " + MyDB, "", "")
        if response, err := conn.Query(q); err == nil && response.Error() == nil {
        }
     
        bp, err := client.NewBatchPoints(client.BatchPointsConfig{
            Database:  MyDB,
        })
        if err != nil {
            log.Fatal(err)
        }
     
        tags := map[string]string{"symbol": "GBPUSD"}
        fields := map[string]interface{}{
            "bid":   1.123,
            "ask":  1.124,
        }
     
        pt, err := client.NewPoint("myuser", tags, fields, time.Now())
        if err != nil {
            log.Fatal(err)
        }
        bp.AddPoint(pt)
        if err := conn.Write(bp); err != nil {
            log.Fatal(err)
        }
    }

result
======

    influx.exe
    > show databases
    name: databases
    name
    ----
    _internal
    test
    > use test
    Using database test
    > show measurements
    name: measurements
    name
    ----
    myuser
    > select * from myuser
    name: myuser
    time                ask   bid   symbol
    ----                ---   ---   ------
    1544760151293028800 1.124 1.123 GBPUSD
    >
