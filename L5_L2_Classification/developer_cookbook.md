# Developer Cookbook — K_GRIRDIUM
**Stack:** Python 3.11, PAX 27B, iridium-tools (local), AIOSS_FORMAT
**Domain:** GridIUM: Iridium satellite mesh protocol analysis for sovereign off-grid Anticloud

## Iridium SBD message
```python
from k_grirdium import IridiumSBDClient

client = IridiumSBDClient(
    imei="300234010753370",  # RockBLOCK IMEI
    pax_model="./pax-27b-q4.gguf",
    aioss_chain="./grirdium.aioss",
    max_sbd_bytes=340
)

# Send compressed PAX inference result via SBD
result = client.send_pax_inference(
    prompt="Sensor anomaly detected: temp=55C, current=2.1A",
    compress=True
)
print(f"SBD sent: {result.sbd_payload_bytes}B")
print(f"PAX response: {result.pax_response}")
print(f"Chain: {result.chain_hash}")
```

## AIOSS Chain Append
```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()
```
