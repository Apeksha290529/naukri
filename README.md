# naukri

name: Profile refresh

on:
  schedule:
    - cron: "30 3 * * *"   # 03:30 UTC = 09:00 IST, once a day
  workflow_dispatch:        # lets you run it manually from the Actions tab

jobs:
  refresh:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install Playwright
        run: |
          pip install playwright
          playwright install --with-deps chromium

      - name: Run refresh
        env:
          NAUKRI_USER: ${{ secrets.NAUKRI_USER }}
          NAUKRI_PASS: ${{ secrets.NAUKRI_PASS }}
          SHINE_USER: ${{ secrets.SHINE_USER }}
          SHINE_PASS: ${{ secrets.SHINE_PASS }}
        run: python refresh.py

      - name: Upload error screenshots
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: error-screenshots
          path: "*_error.png"
