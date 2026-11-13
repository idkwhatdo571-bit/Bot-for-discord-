const https = require('https');

async function sendChangelog() {
    const webhookUrl = process.env.DISCORD_WEBHOOK_URL;
    if (!webhookUrl) {
        console.error("Missing DISCORD_WEBHOOK_URL secret.");
        process.exit(1);
    }

    // Read environment variables passed from GitHub Actions
    const commitMessage = process.env.COMMIT_MSG || "No commit message provided";
    const commitAuthor = process.env.COMMIT_AUTHOR || "Unknown Author";
    const commitSha = process.env.COMMIT_SHA || "0000000";
    const repoName = process.env.REPO_NAME || "Repository";

    // Build the payload for Discord using an embed
    const payload = JSON.stringify({
        content: "@everyone **New Update Deployed!** 🚀",
        embeds: [
            {
                title: `📦 Update in ${repoName}`,
                description: `**Commit Details:**\n\`\`\`${commitMessage}\`\`\``,
                color: 8388736, // Purple / Electric Violet accent
                fields: [
                    {
                        name: "Author",
                        value: commitAuthor,
                        inline: true
                    },
                    {
                        name: "Commit Hash",
                        value: `\`${commitSha.substring(0, 7)}\``,
                        inline: true
                    }
                ],
                timestamp: new Date().toISOString()
            }
        ]
    });

    const url = new URL(webhookUrl);
    const options = {
        hostname: url.hostname,
        path: url.pathname + url.search,
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Content-Length': Buffer.byteLength(payload)
        }
    };

    const req = https.request(options, (res) => {
        let responseBody = '';
        res.on('data', (chunk) => { responseBody += chunk; });
        res.on('end', () => {
            if (res.statusCode >= 200 && res.statusCode < 300) {
                console.log("Successfully posted changelog to Discord!");
            } else {
                console.error(`Failed to post. Status: ${res.statusCode}, Response: ${responseBody}`);
                process.exit(1);
            }
        });
    });

    req.on('error', (error) => {
        console.error(`Error sending webhook: ${error.message}`);
        process.exit(1);
    });

    req.write(payload);
    req.end();
}

sendChangelog();
