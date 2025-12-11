✅ 1. Allow Cloudflare Workers dalam Walled Garden (WAJIB)
Cloudflare Workers biasanya berada bawah IP range:

104.16.0.0/12
172.64.0.0/13

Masukkan dalam walled-garden IP Mikrotik:
/ip hotspot walled-garden ip
add action=allow dst-address=104.16.0.0/12
add action=allow dst-address=172.64.0.0/13

⚠️ Kalau error syntax error, pastikan command tepat begini:
/ip hotspot walled-garden ip add dst-address=104.16.0.0/12 action=allow
/ip hotspot walled-garden ip add dst-address=172.64.0.0/13 action=allow

✅ 2. Allow specific domain Workers.dev (WAJIB)

Jika Worker URL bro:
https://servers-workers-anda.workers.dev

Tambah ke walled-garden layer7:

Step A — Create rule:
/ip hotspot walled-garden
add dst-host=*.workers.dev action=allow
add dst-host=*.cloudflare.com action=allow
add dst-host=servers-workers-anda.workers.dev action=allow

✅ 3. Allow HTTPS (Port 443) traffic ke Worker

Kalau hotspot block HTTPS sebelum login, fetch() akan gagal.

Pastikan:
/ip firewall filter add chain=hotspot dst-port=443 protocol=tcp action=accept

✅ 4. Allow DNS (WAJIB)

Jika hotspot block DNS, domain workers.dev tak boleh resolve.
/ip firewall filter add chain=hotspot dst-port=53 protocol=udp action=accept
/ip firewall filter add chain=hotspot dst-port=53 protocol=tcp action=accept


5. Testing — Verify dari Mikrotik terminal

Test DNS resolve:
/tool dns-update
ping workers.dev

Test HTTPS:

/tool fetch url=https://workers.dev output=none

Kalau dapat “status: finished”, bermakna OK.

🔥 6. Setting paling penting untuk login.html bro
Setting A — Allow HTML + JS dalam /hotspot

/ip hotspot profile set hsprof1 html-directory=hotspot


Setting B — Redirect sukses

Dalam login.html bro, Worker /status URL digunakan:

set redirect-url="https://servers-workers-anda.workers.dev/status"

Atau dari CLI:


/ip hotspot profile set hsprof1 login-by=mac,http-chap,http-redirection


🔍 7. Confirm Worker API dalam browser (tanpa login)

Buka browser HP:
https://servers-workers-anda.workers.dev/tls-info

Kalau dapat output JSON seperti:

{
 "tlsVersion": "TLSv1.3",
 "tlsCipher": "AES128-GCM",
 "clientIP": "xx.xx.xx.xx"
}

= Mikrotik allow Workers SUCCESS.


















