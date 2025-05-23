## JIRA Integration API
A FastAPI-based RESTful API for interacting with JIRA to fetch and manage boards, epics, stories, tasks, descriptions, comments, attachments, and project users. The API provides endpoints to retrieve hierarchical data (boards → epics → stories → tasks/subtasks), manage issues, and save data to a JSON file.

## Overview
This project is a Python-based FastAPI application that integrates with the JIRA REST API to fetch and manage project data. It allows users to:
Retrieve JIRA boards, epics, stories, tasks, and subtasks in a hierarchical structure.
Fetch and update issue descriptions, comments, and attachments.
Create, update, and delete issues.
Retrieve users associated with a JIRA project.
Save the board-epic-story-task hierarchy to a JSON file.
The API uses the Atlassian JIRA REST API (version 3) and supports robust error handling, logging, and Atlassian Document Format (ADF) parsing for descriptions and comments.

## Features
Hierarchical Data Retrieval: Fetch boards, epics, stories, tasks, and subtasks with associated metadata (descriptions, comments, attachments).
Issue Management: Create, read, update, and delete JIRA issues.
Metadata Support: Fetch and update issue descriptions, comments, and attachments.
Project Users: Retrieve users associated with a JIRA project.
Data Export: Save the board hierarchy to a JSON file.
Robust Error Handling: Handles HTTP errors (401, 403, 404) and provides detailed debug logs.
Environment Configuration: Uses a .env file for secure configuration of JIRA credentials.

## Requirements
To run the JIRA Integration API, ensure you have the following:

## Software
Python: Version 3.8 or higher
pip: Python package manager
JIRA Account: Access to a JIRA Cloud instance with an API token and appropriate permissions
Python Dependencies
The following Python packages are required. Install them using the provided requirements.txt:

fastapi: For building the REST API
uvicorn: ASGI server for running the FastAPI application
python-dotenv: For loading environment variables from a .env file
requests: For making HTTP requests to the JIRA API
Create a requirements.txt file with the following content:



fastapi==0.115.0
uvicorn==0.30.6
python-dotenv==1.0.1
requests==2.32.3
Install the dependencies:



pip install -r requirements.txt


JIRA API Access
JIRA Email: The email address associated with your JIRA account.
JIRA API Token: Generated from your Atlassian account (see Setup Instructions).
JIRA Domain: Your JIRA instance domain (e.g., yourcompany.atlassian.net).
Permissions: Ensure the JIRA account has permissions to:
View and manage issues (epics, stories, tasks, subtasks).
Access boards and projects.
Read descriptions, comments, and attachments.
Create and update issues, comments, and attachments (if using those endpoints).
Setup Instructions
Clone the Repository (if using version control):



git clone <repository-url>
cd <repository-directory>

Create a .env File:
In the project root directory (e.g., D:\projects\nexusai), create a file named .env.
Add the following content with your JIRA credentials:

JIRA_EMAIL=your-email@domain.com
JIRA_API_TOKEN=your-api-token
JIRA_DOMAIN=your-domain.atlassian.net
Example:
JIRA_EMAIL=john.doe@example.com
JIRA_API_TOKEN=ATATT3xFfGF0abcdefghijklmnopqrstuvwxyz1234567890
JIRA_DOMAIN=mycompany.atlassian.net
Generate a JIRA API Token:
Log in to https://id.atlassian.com.
Navigate to Security > Create and manage API tokens.
Click Create API token, provide a label (e.g., FastAPI Integration), and copy the token.
Paste the token into the .env file as JIRA_API_TOKEN.
## Install Dependencies:
Ensure Python 3.8+ is installed.
## Install the required packages:

pip install -r requirements.txt
## Verify Permissions:
Confirm that the JIRA account associated with JIRA_EMAIL has access to the projects, boards, and issues you intend to query.
Ensure permissions to view descriptions, comments, and attachments, and to create/update issues if needed.


## API Endpoints
Below is a summary of the key API endpoints:


Method	Endpoint	Description
GET	/boards	Fetch all JIRA boards
GET	/boards/{board_id}/epics	Fetch epics for a specific board with metadata (description, comments, attachments)
GET	/epics/{epic_key}/stories	Fetch stories and metadata for a specific epic
GET	/stories/{story_key}/tasks	Fetch tasks and subtasks linked to a story
GET	/teams/project?project_key={key}	Fetch users in a JIRA project
GET	/hierarchy	Fetch the full board-epic-story-task hierarchy
GET	/hierarchy/save	Save the hierarchy to jira_hierarchy.json
GET	/issues/{issue_key}/description	Fetch an issue's description
PUT	/issues/{issue_key}/description	Update an issue's description
GET	/issues/{issue_key}/comments	Fetch comments for an issue
POST	/issues/{issue_key}/comments	Add a comment to an issue
GET	/issues/{issue_key}/attachments	Fetch attachments for an issue
POST	/issues/{issue_key}/attachments	Add an attachment to an issue
POST	/issues	Create a new issue
GET	/issues?project_key={key}	Fetch all issues in a project
PUT	/issues/{issue_id}	Update an issue's summary
DELETE	/issues/{issue_id}	Delete an issue
GET	/issues/{issue_key}	Fetch issue details (summary, description, comments, attachments)


## Code Explanation
The JIRA Integration API is built using FastAPI and interacts with the JIRA REST API (version 3). Below is an overview of the code's structure and functionality:

### Key Components
Environment Setup:
Uses python-dotenv to load JIRA_EMAIL, JIRA_API_TOKEN, and JIRA_DOMAIN from a .env file.
Validates these variables at startup to prevent runtime errors.
JIRA API Integration:
Uses the requests library with HTTPBasicAuth for authentication.
The safe_request helper function handles HTTP requests, logs responses, and raises specific errors for 401 (authentication), 403 (permission), and 404 (not found) status codes.
Data Models:
Pydantic models (BoardModel, EpicModel, StoryModel, TaskModel, SubtaskModel) define the structure of the hierarchical data.
Models include fields for IDs, keys, summaries, descriptions, comments, attachments, and relationships (e.g., epics → stories → tasks).
Helper Functions:
extract_description: Parses Atlassian Document Format (ADF) descriptions, handling null or malformed structures.
extract_comments: Parses ADF comments, extracting author, body, and creation date.
search_issues: Executes JQL queries to fetch issues with specified fields (summary, subtasks, description, comments, attachments).
get_epic_link_field_id: Dynamically retrieves the "Epic Link" field ID, with a fallback to parent-based queries if not found.
Endpoints:
Hierarchy Retrieval: The /hierarchy endpoint fetches boards, epics, stories, tasks, and subtasks, including metadata (descriptions, comments, attachments).
Issue Management: Endpoints for creating, updating, deleting, and fetching issue details.
Metadata Handling: Dedicated endpoints for fetching and updating descriptions, comments, and attachments.
Project Users: The /teams/project endpoint retrieves users associated with a project.
Error Handling:
Robust error handling for HTTP errors, network issues, and JIRA-specific errors.
Detailed debug logging to trace API responses and errors.
Data Export:
The /hierarchy/save endpoint saves the hierarchical data to jira_hierarchy.json.

## Workflow
Authentication: Uses JIRA_EMAIL and JIRA_API_TOKEN for HTTP Basic Authentication.
API Requests: Sends requests to JIRA's REST API endpoints (e.g., /rest/api/3/issue, /rest/agile/1.0/board).
Data Processing: Parses ADF for descriptions and comments, extracts relevant fields, and structures data into Pydantic models.
Response Delivery: Returns JSON responses conforming to the defined models, with error messages for failed requests.


## Output 

[
  {
    "id": 1,
    "name": "SCRUM board",
    "epics": [
      {
        "id": "10000",
        "name": "Build the Core Chatbot System",
        "description": "Core functioning of chatbot that can respond based on document search + OpenAI.",
        "comments": [
          {
            "author": "kunal j",
            "body": "The core chatbot system is in the planning phase, focusing on integrating document search with OpenAI.",
            "created": "2025-05-22T17:28:43.687+0530"
          }
        ],
        "attachments": [
          {
            "filename": "Screenshot 2024-12-10 172046.png",
            "content": "https://jkunal637-1745945445776.atlassian.net/rest/api/3/attachment/content/10066",
            "created": "2025-05-22T17:30:05.103+0530"
          }
        ],
        "stories": [
          {
            "id": "10049",
            "key": "SCRUM-15",
            "summary": "Develop Chat API",
            "description": "Support features like authentication, persistent sessions, and AI integration.",
            "comments": [
              {
                "author": "kunal j",
                "body": "Chat API development is in planning, considering authentication and AI integration.",
                "created": "2025-05-23T09:57:42.323+0530"
              }
            ],
            "attachments": [
              {
                "filename": "Screenshot 2024-12-10 172046.png",
                "content": "https://jkunal637-1745945445776.atlassian.net/rest/api/3/attachment/content/10099",
                "created": "2025-05-23T10:01:54.818+0530"
              }
            ],
            "tasks": [
              {
                "id": "10051",
                "key": "SCRUM-16",
                "summary": "Design Chat REST API",
                "description": "No description",
                "comments": [],
                "attachments": [],
                "subtasks": [
                  {
                    "id": "10053",
                    "key": "SCRUM-17",
                    "summary": "Define API endpoints (POST /chat, GET /history)",
                    "description": "No description",
                    "comments": [],
                    "attachments": []
                  }
                ]
              }
            ]
          }
  
## License
This project is licensed under the MIT License. See the LICENSE file for details.

