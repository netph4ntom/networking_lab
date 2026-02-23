## Proses Routing
> adalah proses untuk menentukan jalur terbaik untuk mengirimkan packet dari sumber ke tujuan.

### Step by step
1. PC cek apakah local atau beda network, disini pc cek subnetnya kalau tujuan beda network maka kirim ke default gateway.
2. Router terima paket, baca destination IP nya. Misalnya destination IP nya 192.168.10.5.
3. Router lookup ke routing table. Router akan cari entry yang paling cocok, ini namanya Longest Prefix Match (LPM). Router selalu pilih route yang paling spesifik.
Contoh di routing table ada dua entry:
- 192.168.0.0/16 → lewat interface A
- 192.168.10.0/24 → lewat interface B
Router bakal pilih /24 karena lebih spesifik.
4. Router forward paket ke next-hop. Next-hop itu basically "router berikutnya" yang harus dilalui paket sebelum sampai tujuan.
5. Re encapsulation, router akan menghapus frame lama dan membukus ulang dengan frame baru. Lalu kirim keluar interface tujuan.
6. Kalau gak ada entry yang cocok, paket di-drop. Kecuali ada default route (0.0.0.0/0) yang jadi "jalur terakhir" kalau gak ada yang match. Lalu router akan mengirimkan pesan ICMP ke sumber / source IP.

## Administrative Distance & Metric
``Administrative``Distance (AD) dipake kalau ada dua routing protocol berbeda yang sama-sama tau satu network. Router perlu mutusin mana yang lebih dipercaya.

- Connected interface: AD 0 (paling dipercaya)
- Static route: AD 1
- OSPF: AD 110
- RIP: AD 120

Makin kecil AD-nya, makin dipercaya

``Metric`` dipake kalau dalam satu protocol ada beberapa jalur ke tujuan yang sama. Masing-masing protocol punya cara hitung metric yang beda — OSPF pake cost, RIP pake hop count, EIGRP kombinasi bandwidth + delay.