# Google Chat Notify Action

Send GitHub Actions build/deploy notifications to Google Chat using **cards**.

## Usage

```yaml
name: Build and Notify

on:
  push:
    branches:
      - dev

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Build
        run: |
          echo "Running build..."
          npm install
          npm run build

      - name: Notify Google Chat
        if: always()
        uses: your-org/google-chat-notify-action@v1
        with:
          webhook-url: ${{ secrets.GOOGLE_CHAT_WEBHOOK }}
```
