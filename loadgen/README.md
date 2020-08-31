jq -ncM 'while(true; .+1) | {method: "GET", url: ("https://hamburg-main.tdg.mobiledgex.net:10001/posts/"+ (.| tostring))}' |
  vegeta attack -rate=50/s -lazy -format=json -duration=30s | \
  tee results.bin | \
  vegeta reportjq -ncM 'while(true; .+1) | {method: "POST", url: "https://hamburg-main.tdg.mobiledgex.net:10001/posts", body: {id: .} | @base64 }' | \
  vegeta attack -rate=50/s -lazy -format=json -duration=30s | \
  tee results.bin | \
  vegeta report