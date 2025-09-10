# Google Chat Notify Action

Send GitHub Actions build/deploy notifications to Google Chat using **cards**.

## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Collect commits
        id: commits
        run: |
          echo "list<<EOF" >> $GITHUB_OUTPUT
          for commit in $(jq -r '.commits[] | @base64' <<< '${{ toJson(github.event.commits) }}'); do
            _jq() { echo $commit | base64 --decode | jq -r $1; }
            SHA=$(_jq '.id' | cut -c1-7)
            MESSAGE=$(_jq '.message' | head -c 80)
            AUTHOR=$(_jq '.author.name')
            echo "- [${SHA}](https://github.com/${{ github.repository }}/commit/${SHA}): ${MESSAGE} (by ${AUTHOR})" >> $GITHUB_OUTPUT
          done
          echo "EOF" >> $GITHUB_OUTPUT

      - name: Notify Google Chat
        uses: your-org/google-chat-notify-action@v1
        with:
          webhook-url: ${{ secrets.GOOGLE_CHAT_WEBHOOK }}
          status: ${{ job.status }}
          branch: ${{ github.ref_name }}
          actor: ${{ github.actor }}
          repository: ${{ github.repository }}
          commits: ${{ steps.commits.outputs.list }}
