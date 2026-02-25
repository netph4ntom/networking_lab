## Default Routing
> Merupakan route atau jalur yang dikonfigurasi ketika detinasi suatu network tidak diketahui. Default routing ini menggunakan network ``0.0.0.0`` sebagai destinasinya. Network ``0.0.0.0`` itu bisa mencakup semua network yang memang benar -  benar tidak ada di table routing. Misalnya internet.

```bash
# ip route 0.0.0.0 0.0.0.0 fa0/0
atau
# ip route 0.0.0.0 0.0.0.0 10.10.10.1
```