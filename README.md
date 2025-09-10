# Google Chat Notification Action

A GitHub Action to send build status notifications to Google Chat in a beautiful card format. This action shows who triggered the build, the status (success/failure), commit messages on success, or failure reasons on failure.

## Features

- 🎨 **Beautiful Cards**: Rich card format with icons and structured information
- ✅ **Success Notifications**: Shows commit messages for successful builds
- ❌ **Failure Notifications**: Shows failure reasons and details
- 👤 **Actor Information**: Displays who triggered the build
- 🔗 **Direct Links**: Quick access to the GitHub Actions run
- 🎯 **Flexible**: Works with any GitHub event (push, pull_request, etc.)

## Setup

### 1. Get Google Chat Webhook URL

1. Go to your Google Chat space
2. Click on the space name → **Apps & integrations** → **Webhooks**
3. Click **Add webhook**
4. Give it a name and avatar, then click **Save**
5. Copy the webhook URL

The URL will look like:

```
https://chat.googleapis.com/v1/spaces/SPACE_ID/messages?key=WEBHOOK_TOKEN
```

### 2. Extract Token and Space ID

From the webhook URL, extract:

- **Space ID**: The part after `/spaces/` and before `/messages`
- **Token**: The part after `?key=`

### 3. Add Secrets

Add these secrets to your GitHub repository:

- `GOOGLE_CHAT_TOKEN`: The webhook token
- `GOOGLE_CHAT_SPACE_ID`: The space ID

## Usage

### Basic Usage

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build

      - name: Notify Google Chat
        if: always() # This ensures notification is sent regardless of build result
        uses: shreyngd/github-action-notify-google-chat@v1
        with:
          google_chat_token: ${{ secrets.GOOGLE_CHAT_TOKEN }}
          space_id: ${{ secrets.GOOGLE_CHAT_SPACE_ID }}
          status: ${{ job.status }}
```

### Advanced Usage with Custom Messages

```yaml
- name: Notify Google Chat on Success
  if: success()
  uses: shreyngd/github-action-notify-google-chat@v1
  with:
    google_chat_token: ${{ secrets.GOOGLE_CHAT_TOKEN }}
    space_id: ${{ secrets.GOOGLE_CHAT_SPACE_ID }}
    status: "success"
    job_name: "Production Build"

- name: Notify Google Chat on Failure
  if: failure()
  uses: shreyngd/github-action-notify-google-chat@v1
  with:
    google_chat_token: ${{ secrets.GOOGLE_CHAT_TOKEN }}
    space_id: ${{ secrets.GOOGLE_CHAT_SPACE_ID }}
    status: "failure"
    job_name: "Production Build"
    failure_reason: "Build failed due to test failures. Please check the logs for more details."
```

## Inputs

| Input               | Description                                         | Required | Default                    |
| ------------------- | --------------------------------------------------- | -------- | -------------------------- |
| `google_chat_token` | Google Chat webhook token                           | ✅       | -                          |
| `space_id`          | Google Chat space ID                                | ✅       | -                          |
| `status`            | Build status (success, failure, cancelled, skipped) | ✅       | `${{ job.status }}`        |
| `job_name`          | Job name to display                                 | ❌       | `${{ github.job }}`        |
| `actor`             | User who triggered the build                        | ❌       | `${{ github.actor }}`      |
| `repository`        | Repository name                                     | ❌       | `${{ github.repository }}` |
| `branch`            | Branch name                                         | ❌       | `${{ github.ref_name }}`   |
| `commit_sha`        | Commit SHA                                          | ❌       | `${{ github.sha }}`        |
| `failure_reason`    | Custom failure reason                               | ❌       | 'Build failed'             |

## Card Examples

### Success Card

- Shows ✅ success icon
- Displays recent commit messages
- Includes build details (actor, job, branch, commit)
- Provides "View Build" button

### Failure Card

- Shows ❌ failure icon
- Displays failure reason
- Includes build details (actor, job, branch, commit)
- Provides "View Build" button

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/shreyngd/github-action-notify-google-chat/issues) page
2. Create a new issue with detailed information
3. Include your workflow file and any error messages
