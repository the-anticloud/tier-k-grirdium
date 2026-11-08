# Deploy Guide — K_GRIRDIUM
**Tier:** TIER_8_RF_MESH_DEFENSE | **Stack:** Python 3.11, PAX 27B, iridium-tools (local), AIOSS_FORMAT
**Air-gap capable after initial setup.**

## Prerequisites
Python 3.11+, iridium-tools. Iridium modem (e.g., RockBLOCK 9603) or Iridium SBD simulator. 4GB RAM.

## Environment
Iridium modem (RockBLOCK 9603) or SBD simulator. Low-bandwidth mode: PAX compressed output ≤340 bytes.

## AIOSS Integration
```bash
aioss init --module K_GRIRDIUM --output ./k_grirdium.aioss
aioss append --chain ./k_grirdium.aioss --payload ./output.bin --module K_GRIRDIUM
aioss verify --chain ./k_grirdium.aioss
```

## Air-Gap Setup
```bash
pip download -r requirements.txt -d ./wheels/
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="K_GRIRDIUM",
    aioss_chain="./K_GRIRDIUM.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./K_GRIRDIUM.aioss --verbose
python -m K_GRIRDIUM.tests.smoke
```
