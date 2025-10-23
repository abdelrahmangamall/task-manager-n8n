# ⚡ Smart Task Manager - n8n Integration

A modern web application that integrates with n8n workflow automation to manage tasks efficiently. This project demonstrates seamless integration between a frontend interface and n8n backend workflows, with automatic data persistence to Google Sheets.

## 🎯 Project Overview

This project was built as part of the LyRise internship technical assessment. It showcases:
- Real-time communication between web interface and n8n workflows
- Automated data collection and storage in Google Sheets
- Modern, responsive UI with gradient design
- Persistent task storage using localStorage
- Live workflow execution feedback

## 🛠️ Tech Stack

### Frontend
- **HTML5** - Semantic markup and structure
- **CSS3** - Modern styling with gradients, animations, and flexbox
- **Vanilla JavaScript** - No frameworks, pure JS for simplicity
- **LocalStorage API** - Client-side data persistence

### Backend & Automation
- **n8n** - Workflow automation platform
- **Google Sheets API** - Data storage and management
- **Webhooks** - Real-time communication between frontend and n8n

## ✨ Features

### Core Features
- ✅ Create tasks with title, description, priority, and email
- ✅ Real-time webhook integration with n8n
- ✅ Automatic data saving to Google Sheets
- ✅ Task persistence across browser sessions
- ✅ Responsive design for mobile and desktop
- ✅ Visual feedback for all user actions

### Bonus Features
- 🎁 Live workflow execution status
- 🎁 Response data from n8n displayed on the website
- 🎁 State changes based on workflow execution
- 🎁 Task list with animations
- 🎁 Clear all tasks functionality

## 📋 Prerequisites

Before running this project, make sure you have:

1. **n8n Account** - [Sign up at n8n.cloud](https://n8n.cloud) (free tier available)
2. **Google Account** - For Google Sheets integration
3. **Web Browser** - Modern browser with JavaScript enabled
4. **GitHub Account** - For hosting (optional)

## 🚀 Setup Instructions

### Step 1: Set Up Google Sheets

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet named "Task Manager"
3. In the first row, add these headers:
   ```
   Title | Description | Priority | Email | Timestamp | Status
   ```
4. Note the spreadsheet name for n8n configuration

### Step 2: Configure n8n Workflow

1. **Log in to n8n** at [app.n8n.cloud](https://app.n8n.cloud)

2. **Create a new workflow** with these nodes:

#### Node 1: Webhook (Trigger)
- **Node Type:** Webhook
- **HTTP Method:** POST
- **Path:** `task-manager`
- **Response Mode:** "Using 'Respond to Webhook' Node"

#### Node 2: Edit Fields
- **Node Type:** Edit Fields (Data Transformation)
- **Operation:** Add/Update Fields
- **Fields to Add:**
  - `title` → `{{ $json.body.title }}`
  - `description` → `{{ $json.body.description }}`
  - `priority` → `{{ $json.body.priority }}`
  - `email` → `{{ $json.body.email }}`
  - `timestamp` → `{{ $json.body.timestamp }}`
  - `status` → `{{ $json.body.status }}`

#### Node 3: Google Sheets
- **Node Type:** Google Sheets
- **Operation:** Append Row
- **Authentication:** OAuth2 (connect your Google account)
- **Document:** Select "Task Manager" (or your sheet name)
- **Sheet:** sheet1
- **Mapping Column Mode:** Map Each Column Manually
- **Columns:**
  - Title → `{{ $json.title }}`
  - Description → `{{ $json.description }}`
  - Priority → `{{ $json.priority }}`
  - Email → `{{ $json.email }}`
  - Timestamp → `{{ $json.timestamp }}`
  - Status → `{{ $json.status }}`

#### Node 4: Respond to Webhook
- **Node Type:** Respond to Webhook
- **Respond With:** JSON
- **Response Body:**
```json
{
  "status": "success",
  "message": "Task saved to Google Sheets successfully!",
  "taskId": "{{ $json.id }}",
  "timestamp": "{{ new Date().toISOString() }}"
}
```

3. **Connect the nodes:** Webhook → Edit Fields → Google Sheets → Respond to Webhook

4. **Activate the workflow** (toggle switch at top-right)

5. **Copy the Production Webhook URL** (looks like: `https://your-account.app.n8n.cloud/webhook/task-manager`)

### Step 3: Deploy the Website

#### Option A: GitHub Pages (Recommended)

1. **Create a new repository** on GitHub
2. **Upload the `index.html` file**
3. Go to **Settings** → **Pages**
4. Select **Deploy from branch** → **main branch**
5. Click **Save**
6. Your site will be live at: `https://your-username.github.io/repository-name/`

#### Option B: Local Development

1. Download the `index.html` file
2. Open it directly in your browser
3. Or use a local server:
   ```bash
   # Using Python 3
   python -m http.server 8000
   
   # Using Node.js (http-server)
   npx http-server
   ```

### Step 4: Configure the Website

1. Open your deployed website
2. In the **n8n Webhook Configuration** section:
   - Paste your n8n Production Webhook URL
   - The URL will be saved automatically in localStorage
3. Make sure your n8n workflow is **ACTIVE** (green toggle)

## 📖 How to Use

### Creating a Task

1. **Fill in the task form:**
   - **Task Title** (required) - e.g., "Complete project documentation"
   - **Description** (optional) - Additional details about the task
   - **Priority** (required) - Choose Low, Medium, or High
   - **Email** (optional) - For notifications

2. **Click "Create Task & Trigger n8n Workflow"**

3. **Wait for confirmation:**
   - ✅ Success message appears
   - Task appears in "Recent Tasks" section
   - Data is saved to Google Sheets automatically

### Managing Tasks

- **View Tasks:** All tasks appear in the "Recent Tasks" section
- **Persistent Storage:** Tasks remain even after page reload
- **Clear All:** Click the "Clear All" button to remove all tasks from the website (Google Sheets data remains)

## 🎨 Project Structure

```
task-manager-n8n/
├── index.html           # Main HTML file with embedded CSS and JS
├── README.md           # This documentation file
└── screenshots/        # Project screenshots (optional)
    ├── workflow.png
    ├── website.png
    └── sheets.png
```

## 🔄 Workflow Diagram

```
┌─────────────┐
│   Website   │
│  (Frontend) │
└──────┬──────┘
       │ POST /webhook/task-manager
       │ {title, description, priority, email, timestamp, status}
       ↓
┌─────────────┐
│   Webhook   │
│  (n8n Node) │
└──────┬──────┘
       │
       ↓
┌──────────────┐
│ Edit Fields  │
│ (Transform)  │
└──────┬───────┘
       │
       ↓
┌──────────────────┐
│  Google Sheets   │
│  (Append Row)    │
└──────┬───────────┘
       │
       ↓
┌───────────────────┐
│ Respond to        │
│ Webhook (JSON)    │
└──────┬────────────┘
       │ {status: "success", message: "..."}
       ↓
┌─────────────┐
│   Website   │
│  (Updates)  │
└─────────────┘
```

## 🎥 Demo Video

[Insert your screen recording link here]

**Video Contents:**
- Website interface walkthrough
- Creating multiple tasks with different priorities
- n8n workflow execution in real-time
- Google Sheets data verification
- Response feedback from n8n to website

## 📸 Screenshots

### Website Interface
[Add screenshot of the main interface]

### n8n Workflow
[Add screenshot of your n8n workflow canvas]

### Google Sheets Integration
[Add screenshot of populated Google Sheets]

### Success Message
[Add screenshot of success notification]

## 🐛 Troubleshooting

### Common Issues

**Problem:** "Failed to connect to n8n webhook"
- **Solution:** 
  - Check if workflow is ACTIVE (green toggle)
  - Verify webhook URL is correct (should be Production URL, not Test URL)
  - Check CORS settings in n8n (should allow your domain)

**Problem:** "Data not appearing in Google Sheets"
- **Solution:**
  - Verify Google Sheets authentication in n8n
  - Check column mapping in Google Sheets node
  - Ensure sheet name matches exactly

**Problem:** "Tasks disappear after reload"
- **Solution:**
  - Check browser's localStorage settings
  - Make sure cookies/storage are not blocked
  - Try a different browser

**Problem:** "Timestamp validation error in Google Sheets"
- **Solution:**
  - Format the Timestamp column as "Plain text" in Google Sheets
  - Or remove data validation from the Timestamp column

## 🎯 Key Learning Outcomes

Through this project, I demonstrated:

1. **Webhook Integration** - Bidirectional communication between frontend and backend
2. **API Integration** - Google Sheets API through n8n
3. **Data Persistence** - Both client-side (localStorage) and server-side (Google Sheets)
4. **Error Handling** - Graceful handling of network and validation errors
5. **User Experience** - Responsive design with visual feedback
6. **Workflow Automation** - n8n workflow design and execution
7. **Documentation** - Clear, comprehensive technical documentation

## 🔮 Future Enhancements

Potential improvements for this project:

- [ ] User authentication system
- [ ] Task editing and deletion from the UI
- [ ] Email notifications using n8n
- [ ] Task status tracking (pending → in-progress → completed)
- [ ] Task filtering and search functionality
- [ ] Dark mode toggle
- [ ] Export tasks to PDF
- [ ] Real-time collaboration features
- [ ] Integration with calendar apps
- [ ] Mobile app version

## 📝 License

This project is created for educational purposes as part of the LyRise internship technical assessment.

## 👤 Author

**Abdelrahman Gamal**
- GitHub: [@abdelrahmangamall](https://github.com/abdelrahmangamall)
- Email: [Your Email]
- LinkedIn: [Your LinkedIn Profile]

## 🙏 Acknowledgments

- **LyRise Team** - For the internship opportunity and technical challenge
- **n8n Community** - For excellent documentation and support
- **Google** - For the Sheets API integration

## 📞 Support

For questions or issues, please:
1. Check the Troubleshooting section above
2. Review n8n documentation at [docs.n8n.io](https://docs.n8n.io)
3. Contact me via email or GitHub issues

---

**Built with ❤️ using n8n workflow automation**

*Last Updated: October 2025*
