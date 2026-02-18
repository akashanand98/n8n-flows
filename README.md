# n8n-flows
Repository that will consist of n8n flows for modern tasks and usecases

## Workflows

### LC Code Flow
**Purpose:** Daily LeetCode problem delivery with AI-generated solutions and explanations

**Schedule:** Runs daily at 8:00 AM

**Flow:**
1. **Schedule Trigger** - Triggers workflow at 8 AM daily
2. **Code in JavaScript** - Generates random number (100-999) to select a random LeetCode problem
3. **HTTP Request** - Queries LeetCode GraphQL API to fetch problem details
4. **AI Agent** - Uses OpenAI GPT-4o-mini to generate comprehensive solution including:
   - Problem understanding
   - Optimal approach explanation
   - Python solution with comments
   - Complexity analysis
   - Edge cases
   - Optimization notes
5. **Markdown** - Converts AI response from Markdown to HTML
6. **Send a message** - Emails formatted solution to akashbavaanand@gmail.com via Gmail

**Requirements:**
- OpenAI API credentials
- Gmail OAuth2 credentials
