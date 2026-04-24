<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>API Documentation</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Courier New', monospace; background: #f8f7f4; color: #1a1a18; min-height: 100vh; }
  a { color: inherit; }

  .layout { display: flex; min-height: 100vh; }

  /* Sidebar */
  .sidebar {
    width: 220px; flex-shrink: 0;
    background: #fff; border-right: 0.5px solid #ddd;
    position: sticky; top: 0; height: 100vh; overflow-y: auto;
    padding: 1.5rem 0;
  }
  .sidebar-logo { padding: 0 1.25rem 1.25rem; border-bottom: 0.5px solid #eee; margin-bottom: .75rem; }
  .sidebar-logo .api-name { font-size: 15px; font-weight: 600; font-family: sans-serif; color: #1a1a18; }
  .sidebar-logo .api-version { font-size: 11px; color: #888; margin-top: 2px; }
  .tag-group { font-size: 10px; font-weight: 600; color: #aaa; text-transform: uppercase; letter-spacing: .08em; padding: .75rem 1.25rem .3rem; font-family: sans-serif; }
  .nav-item {
    display: flex; align-items: center; gap: 8px;
    padding: 6px 1.25rem; cursor: pointer;
    border-left: 2px solid transparent;
    color: #555; font-size: 12px; text-decoration: none;
    transition: background .12s, color .12s;
  }
  .nav-item:hover { background: #f4f3f0; color: #1a1a18; }
  .nav-item.active { color: #1a1a18; border-left-color: #185FA5; background: #f0f4fa; }

  /* Method badges */
  .badge {
    font-size: 10px; font-weight: 600; padding: 2px 6px;
    border-radius: 4px; flex-shrink: 0; font-family: monospace; letter-spacing: .02em;
  }
  .get    { background: #E6F1FB; color: #185FA5; }
  .post   { background: #EAF3DE; color: #3B6D11; }
  .put    { background: #FAEEDA; color: #854F0B; }
  .delete { background: #FCEBEB; color: #A32D2D; }
  .patch  { background: #EEEDFE; color: #534AB7; }

  /* Main content */
  .main { flex: 1; max-width: 860px; padding: 2.5rem 2rem; }

  .api-header { margin-bottom: 2rem; padding-bottom: 1.5rem; border-bottom: 0.5px solid #ddd; }
  .api-title { font-size: 24px; font-weight: 600; font-family: sans-serif; color: #1a1a18; margin-bottom: 6px; }
  .api-desc { font-size: 14px; color: #555; line-height: 1.65; font-family: sans-serif; margin-bottom: 1rem; }
  .api-meta { display: flex; gap: 10px; flex-wrap: wrap; align-items: center; }
  .version-pill { font-size: 11px; padding: 3px 9px; border-radius: 4px; background: #E6F1FB; color: #185FA5; font-family: monospace; }
  .base-url {
    font-size: 12px; color: #444; font-family: monospace;
    background: #f4f3f0; padding: 5px 12px;
    border-radius: 6px; border: 0.5px solid #ddd;
  }

  /* Endpoint cards */
  .endpoint-card { border: 0.5px solid #ddd; border-radius: 10px; margin-bottom: 12px; overflow: hidden; background: #fff; }
  .endpoint-header {
    display: flex; align-items: center; gap: 12px;
    padding: 13px 16px; cursor: pointer; background: #fff;
    transition: background .12s; user-select: none;
  }
  .endpoint-header:hover { background: #f8f7f4; }
  .endpoint-path { font-family: monospace; font-size: 13px; color: #1a1a18; flex: 1; }
  .endpoint-summary { font-size: 12px; color: #888; font-family: sans-serif; }
  .chevron { font-size: 10px; color: #bbb; margin-left: auto; transition: transform .2s; }
  .chevron.open { transform: rotate(180deg); }

  .endpoint-body { border-top: 0.5px solid #eee; padding: 18px 20px; display: none; }
  .endpoint-body.open { display: block; }

  .section-label {
    font-size: 10px; font-weight: 600; color: #aaa;
    letter-spacing: .07em; text-transform: uppercase;
    margin-bottom: .5rem; font-family: sans-serif;
  }
  .desc { font-size: 13px; color: #555; line-height: 1.65; margin-bottom: 1rem; font-family: sans-serif; }

  .auth-note {
    display: flex; align-items: center; gap: 7px;
    font-size: 12px; color: #555; font-family: sans-serif;
    padding: 7px 12px; background: #f8f7f4;
    border-radius: 6px; margin-bottom: 1rem;
    border: 0.5px solid #e0ddd6;
  }

  .params-table { width: 100%; border-collapse: collapse; font-size: 12px; margin-bottom: 1rem; }
  .params-table th {
    text-align: left; font-size: 10px; font-weight: 600; color: #aaa;
    text-transform: uppercase; letter-spacing: .06em;
    padding: 5px 8px; border-bottom: 0.5px solid #eee; font-family: sans-serif;
  }
  .params-table td { padding: 7px 8px; border-bottom: 0.5px solid #f0efec; vertical-align: top; font-family: sans-serif; }
  .params-table tr:last-child td { border-bottom: none; }
  .param-name { font-family: monospace; color: #1a1a18; }
  .param-type { font-family: monospace; color: #888; font-size: 11px; }
  .req-badge { font-size: 10px; padding: 2px 6px; border-radius: 3px; background: #FCEBEB; color: #A32D2D; }
  .opt-badge { font-size: 10px; padding: 2px 6px; border-radius: 3px; background: #f4f3f0; color: #888; }

  .code-block {
    background: #f4f3f0; border: 0.5px solid #e0ddd6;
    border-radius: 6px; padding: 14px 16px;
    font-family: monospace; font-size: 12px; color: #333;
    white-space: pre; overflow-x: auto; margin-bottom: 1rem; line-height: 1.75;
  }

  .divider { height: 0.5px; background: #eee; margin: 1rem 0; }

  .response-tabs { display: flex; gap: 6px; margin-bottom: .75rem; flex-wrap: wrap; }
  .res-tab {
    font-size: 11px; padding: 4px 10px; border-radius: 5px;
    border: 0.5px solid #ddd; cursor: pointer;
    font-family: monospace; color: #888; background: #fff; transition: .12s;
  }
  .res-tab:hover { background: #f4f3f0; }
  .tab-2xx { border-color: #b8d9a4; color: #3B6D11; background: #EAF3DE; }
  .tab-4xx { border-color: #f5c89a; color: #854F0B; background: #FAEEDA; }
  .tab-5xx { border-color: #f5b8b8; color: #A32D2D; background: #FCEBEB; }

  /* Search */
  .search-wrap { padding: 0 1.25rem .75rem; }
  .search-input {
    width: 100%; font-size: 12px; padding: 6px 10px;
    border: 0.5px solid #ddd; border-radius: 6px;
    background: #f8f7f4; color: #1a1a18; font-family: sans-serif;
    outline: none;
  }
  .search-input:focus { border-color: #185FA5; background: #fff; }
  .search-input::placeholder { color: #bbb; }

  /* Try it panel */
  .try-panel { background: #f4f3f0; border-radius: 6px; padding: 14px; margin-top: 1rem; border: 0.5px solid #e0ddd6; display: none; }
  .try-panel.open { display: block; }
  .try-row { display: flex; gap: 8px; align-items: center; margin-bottom: 10px; }
  .try-method { font-size: 12px; font-weight: 600; font-family: monospace; }
  .try-url { flex: 1; font-size: 12px; padding: 6px 10px; border: 0.5px solid #ddd; border-radius: 5px; font-family: monospace; background: #fff; outline: none; color: #1a1a18; }
  .try-url:focus { border-color: #185FA5; }
  .send-btn {
    font-size: 12px; padding: 6px 14px; border-radius: 5px;
    border: 0.5px solid #185FA5; background: #185FA5; color: #fff;
    cursor: pointer; font-family: sans-serif; transition: .12s;
  }
  .send-btn:hover { background: #0C447C; border-color: #0C447C; }
  .try-response { background: #fff; border: 0.5px solid #ddd; border-radius: 5px; padding: 10px; font-size: 12px; font-family: monospace; white-space: pre; overflow-x: auto; color: #333; line-height: 1.65; min-height: 48px; }
  .try-token-row { display: flex; gap: 8px; align-items: center; margin-bottom: 10px; }
  .try-token-label { font-size: 11px; color: #888; font-family: sans-serif; white-space: nowrap; }
  .try-token { flex: 1; font-size: 11px; padding: 5px 10px; border: 0.5px solid #ddd; border-radius: 5px; font-family: monospace; background: #fff; outline: none; color: #1a1a18; }
  .open-try-btn {
    font-size: 11px; padding: 5px 12px; border-radius: 5px;
    border: 0.5px solid #ddd; background: #fff; color: #555;
    cursor: pointer; font-family: sans-serif; margin-bottom: 4px; transition: .12s;
  }
  .open-try-btn:hover { background: #f4f3f0; }

  @media (max-width: 700px) {
    .sidebar { display: none; }
    .main { padding: 1.25rem 1rem; }
  }
</style>
</head>
<body>

<div class="layout">
  <!-- Sidebar -->
  <nav class="sidebar">
    <div class="sidebar-logo">
      <div class="api-name">My API</div>
      <div class="api-version">v1.0.0</div>
    </div>
    <div class="search-wrap">
      <input class="search-input" type="text" placeholder="Search endpoints..." oninput="filterEndpoints(this.value)" />
    </div>
    <div class="tag-group">Users</div>
    <a class="nav-item active" href="#ep-list-users" onclick="activateNav(this)"><span class="badge get">GET</span> /users</a>
    <a class="nav-item" href="#ep-create-user" onclick="activateNav(this)"><span class="badge post">POST</span> /users</a>
    <a class="nav-item" href="#ep-get-user" onclick="activateNav(this)"><span class="badge get">GET</span> /users/{id}</a>
    <a class="nav-item" href="#ep-update-user" onclick="activateNav(this)"><span class="badge put">PUT</span> /users/{id}</a>
    <a class="nav-item" href="#ep-delete-user" onclick="activateNav(this)"><span class="badge delete">DEL</span> /users/{id}</a>
    <div class="tag-group">Auth</div>
    <a class="nav-item" href="#ep-login" onclick="activateNav(this)"><span class="badge post">POST</span> /auth/login</a>
    <a class="nav-item" href="#ep-refresh" onclick="activateNav(this)"><span class="badge post">POST</span> /auth/refresh</a>
  </nav>

  <!-- Main -->
  <main class="main">

    <div class="api-header">
      <div class="api-title">My API</div>
      <div class="api-desc">Replace this with your API description. Explain what it does, who it's for, and any important usage notes.</div>
      <div class="api-meta">
        <span class="version-pill">v1.0.0</span>
        <span class="base-url">https://api.yourservice.com/v1</span>
      </div>
    </div>

    <!-- LIST USERS -->
    <div id="ep-list-users" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge get">GET</span>
        <span class="endpoint-path">/users</span>
        <span class="endpoint-summary">List all users</span>
        <span class="chevron open">▼</span>
      </div>
      <div class="endpoint-body open">
        <div class="desc">Returns a paginated list of users. Supports filtering by role and status.</div>
        <div class="auth-note">🔒 Requires Bearer token</div>
        <div class="section-label">Query parameters</div>
        <table class="params-table">
          <tr><th>Name</th><th>Type</th><th>Required</th><th>Description</th></tr>
          <tr><td class="param-name">page</td><td class="param-type">integer</td><td><span class="opt-badge">optional</span></td><td>Page number (default: 1)</td></tr>
          <tr><td class="param-name">limit</td><td class="param-type">integer</td><td><span class="opt-badge">optional</span></td><td>Items per page, max 100 (default: 20)</td></tr>
          <tr><td class="param-name">role</td><td class="param-type">string</td><td><span class="opt-badge">optional</span></td><td>Filter by role: admin, user, guest</td></tr>
          <tr><td class="param-name">status</td><td class="param-type">string</td><td><span class="opt-badge">optional</span></td><td>Filter by status: active, inactive</td></tr>
        </table>
        <div class="divider"></div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('list-users','200',this)">200 OK</div>
          <div class="res-tab" onclick="setTab('list-users','400',this)">400 Bad Request</div>
          <div class="res-tab" onclick="setTab('list-users','401',this)">401 Unauthorized</div>
        </div>
        <div id="resp-list-users-200" class="code-block">{
  "data": [
    {
      "id": "usr_01HXYZ",
      "name": "Ada Lovelace",
      "email": "ada@example.com",
      "role": "admin",
      "status": "active",
      "created_at": "2024-01-15T10:30:00Z"
    }
  ],
  "meta": {
    "total": 142,
    "page": 1,
    "limit": 20,
    "has_more": true
  }
}</div>
        <div id="resp-list-users-400" class="code-block" style="display:none">{
  "error": {
    "code": "INVALID_PARAM",
    "message": "Invalid value for 'limit'. Must be between 1 and 100.",
    "param": "limit"
  }
}</div>
        <div id="resp-list-users-401" class="code-block" style="display:none">{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Missing or invalid Authorization header."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-list-users')">Try it out</button>
        <div id="try-list-users" class="try-panel">
          <div class="try-token-row">
            <span class="try-token-label">Bearer token</span>
            <input class="try-token" type="text" placeholder="your_access_token" id="token-list-users" />
          </div>
          <div class="try-row">
            <span class="try-method get" style="color:#185FA5">GET</span>
            <input class="try-url" id="url-list-users" value="https://api.yourservice.com/v1/users?page=1&limit=20" />
            <button class="send-btn" onclick="sendRequest('list-users','GET')">Send</button>
          </div>
          <div class="try-response" id="out-list-users">// Response will appear here</div>
        </div>
      </div>
    </div>

    <!-- CREATE USER -->
    <div id="ep-create-user" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge post">POST</span>
        <span class="endpoint-path">/users</span>
        <span class="endpoint-summary">Create a user</span>
        <span class="chevron">▼</span>
      </div>
      <div class="endpoint-body">
        <div class="desc">Creates a new user account. Sends a verification email upon successful creation.</div>
        <div class="auth-note">🔒 Requires Bearer token — admin role only</div>
        <div class="section-label">Request body</div>
        <table class="params-table">
          <tr><th>Field</th><th>Type</th><th>Required</th><th>Description</th></tr>
          <tr><td class="param-name">name</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>Full name (max 128 chars)</td></tr>
          <tr><td class="param-name">email</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>Valid email address</td></tr>
          <tr><td class="param-name">role</td><td class="param-type">string</td><td><span class="opt-badge">optional</span></td><td>admin, user, guest (default: user)</td></tr>
          <tr><td class="param-name">metadata</td><td class="param-type">object</td><td><span class="opt-badge">optional</span></td><td>Arbitrary key-value pairs</td></tr>
        </table>
        <div class="section-label">Example request</div>
        <div class="code-block">POST /v1/users
Content-Type: application/json
Authorization: Bearer &lt;token&gt;

{
  "name": "Grace Hopper",
  "email": "grace@example.com",
  "role": "user"
}</div>
        <div class="divider"></div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('create-user','201',this)">201 Created</div>
          <div class="res-tab" onclick="setTab('create-user','400',this)">400 Validation Error</div>
          <div class="res-tab" onclick="setTab('create-user','409',this)">409 Conflict</div>
        </div>
        <div id="resp-create-user-201" class="code-block">{
  "id": "usr_02HABC",
  "name": "Grace Hopper",
  "email": "grace@example.com",
  "role": "user",
  "status": "pending_verification",
  "created_at": "2024-06-01T09:00:00Z"
}</div>
        <div id="resp-create-user-400" class="code-block" style="display:none">{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed.",
    "details": [
      { "field": "email", "issue": "Invalid email format." }
    ]
  }
}</div>
        <div id="resp-create-user-409" class="code-block" style="display:none">{
  "error": {
    "code": "EMAIL_EXISTS",
    "message": "A user with this email already exists."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-create-user')">Try it out</button>
        <div id="try-create-user" class="try-panel">
          <div class="try-token-row">
            <span class="try-token-label">Bearer token</span>
            <input class="try-token" type="text" placeholder="your_access_token" id="token-create-user" />
          </div>
          <div class="try-row">
            <span class="try-method" style="color:#3B6D11">POST</span>
            <input class="try-url" id="url-create-user" value="https://api.yourservice.com/v1/users" />
            <button class="send-btn" onclick="sendRequest('create-user','POST')">Send</button>
          </div>
          <div style="margin-bottom:8px">
            <textarea class="try-url" id="body-create-user" rows="5" style="width:100%;resize:vertical">{
  "name": "Grace Hopper",
  "email": "grace@example.com",
  "role": "user"
}</textarea>
          </div>
          <div class="try-response" id="out-create-user">// Response will appear here</div>
        </div>
      </div>
    </div>

    <!-- GET USER -->
    <div id="ep-get-user" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge get">GET</span>
        <span class="endpoint-path">/users/{id}</span>
        <span class="endpoint-summary">Get a user</span>
        <span class="chevron">▼</span>
      </div>
      <div class="endpoint-body">
        <div class="desc">Retrieves a single user by their unique ID.</div>
        <div class="auth-note">🔒 Requires Bearer token</div>
        <div class="section-label">Path parameters</div>
        <table class="params-table">
          <tr><th>Name</th><th>Type</th><th>Required</th><th>Description</th></tr>
          <tr><td class="param-name">id</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>User ID (prefix: usr_)</td></tr>
        </table>
        <div class="divider"></div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('get-user','200',this)">200 OK</div>
          <div class="res-tab" onclick="setTab('get-user','404',this)">404 Not Found</div>
        </div>
        <div id="resp-get-user-200" class="code-block">{
  "id": "usr_01HXYZ",
  "name": "Ada Lovelace",
  "email": "ada@example.com",
  "role": "admin",
  "status": "active",
  "created_at": "2024-01-15T10:30:00Z",
  "updated_at": "2024-05-10T08:12:00Z"
}</div>
        <div id="resp-get-user-404" class="code-block" style="display:none">{
  "error": {
    "code": "NOT_FOUND",
    "message": "No user found with id 'usr_01HXYZ'."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-get-user')">Try it out</button>
        <div id="try-get-user" class="try-panel">
          <div class="try-token-row">
            <span class="try-token-label">Bearer token</span>
            <input class="try-token" type="text" placeholder="your_access_token" id="token-get-user" />
          </div>
          <div class="try-row">
            <span class="try-method" style="color:#185FA5">GET</span>
            <input class="try-url" id="url-get-user" value="https://api.yourservice.com/v1/users/usr_01HXYZ" />
            <button class="send-btn" onclick="sendRequest('get-user','GET')">Send</button>
          </div>
          <div class="try-response" id="out-get-user">// Response will appear here</div>
        </div>
      </div>
    </div>

    <!-- UPDATE USER -->
    <div id="ep-update-user" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge put">PUT</span>
        <span class="endpoint-path">/users/{id}</span>
        <span class="endpoint-summary">Update a user</span>
        <span class="chevron">▼</span>
      </div>
      <div class="endpoint-body">
        <div class="desc">Replaces a user record. All editable fields must be supplied. Use PATCH for partial updates.</div>
        <div class="auth-note">🔒 Requires Bearer token</div>
        <div class="section-label">Request body</div>
        <table class="params-table">
          <tr><th>Field</th><th>Type</th><th>Required</th><th>Description</th></tr>
          <tr><td class="param-name">name</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>Full name</td></tr>
          <tr><td class="param-name">role</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>admin, user, guest</td></tr>
          <tr><td class="param-name">status</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>active, inactive</td></tr>
        </table>
        <div class="divider"></div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('update-user','200',this)">200 OK</div>
          <div class="res-tab" onclick="setTab('update-user','400',this)">400 Validation Error</div>
        </div>
        <div id="resp-update-user-200" class="code-block">{
  "id": "usr_01HXYZ",
  "name": "Ada Lovelace",
  "role": "user",
  "status": "active",
  "updated_at": "2024-06-20T14:55:00Z"
}</div>
        <div id="resp-update-user-400" class="code-block" style="display:none">{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Field 'role' must be one of: admin, user, guest."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-update-user')">Try it out</button>
        <div id="try-update-user" class="try-panel">
          <div class="try-token-row">
            <span class="try-token-label">Bearer token</span>
            <input class="try-token" type="text" placeholder="your_access_token" id="token-update-user" />
          </div>
          <div class="try-row">
            <span class="try-method" style="color:#854F0B">PUT</span>
            <input class="try-url" id="url-update-user" value="https://api.yourservice.com/v1/users/usr_01HXYZ" />
            <button class="send-btn" onclick="sendRequest('update-user','PUT')">Send</button>
          </div>
          <div style="margin-bottom:8px">
            <textarea class="try-url" id="body-update-user" rows="5" style="width:100%;resize:vertical">{
  "name": "Ada Lovelace",
  "role": "user",
  "status": "active"
}</textarea>
          </div>
          <div class="try-response" id="out-update-user">// Response will appear here</div>
        </div>
      </div>
    </div>

    <!-- DELETE USER -->
    <div id="ep-delete-user" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge delete">DELETE</span>
        <span class="endpoint-path">/users/{id}</span>
        <span class="endpoint-summary">Delete a user</span>
        <span class="chevron">▼</span>
      </div>
      <div class="endpoint-body">
        <div class="desc">Permanently deletes a user. This action is irreversible. Returns 204 with no body on success.</div>
        <div class="auth-note">🔒 Requires Bearer token — admin role only</div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('delete-user','204',this)">204 No Content</div>
          <div class="res-tab" onclick="setTab('delete-user','404',this)">404 Not Found</div>
        </div>
        <div id="resp-delete-user-204" class="code-block">// No response body</div>
        <div id="resp-delete-user-404" class="code-block" style="display:none">{
  "error": {
    "code": "NOT_FOUND",
    "message": "No user found with the given id."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-delete-user')">Try it out</button>
        <div id="try-delete-user" class="try-panel">
          <div class="try-token-row">
            <span class="try-token-label">Bearer token</span>
            <input class="try-token" type="text" placeholder="your_access_token" id="token-delete-user" />
          </div>
          <div class="try-row">
            <span class="try-method" style="color:#A32D2D">DELETE</span>
            <input class="try-url" id="url-delete-user" value="https://api.yourservice.com/v1/users/usr_01HXYZ" />
            <button class="send-btn" onclick="sendRequest('delete-user','DELETE')">Send</button>
          </div>
          <div class="try-response" id="out-delete-user">// Response will appear here</div>
        </div>
      </div>
    </div>

    <!-- LOGIN -->
    <div id="ep-login" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge post">POST</span>
        <span class="endpoint-path">/auth/login</span>
        <span class="endpoint-summary">Login</span>
        <span class="chevron">▼</span>
      </div>
      <div class="endpoint-body">
        <div class="desc">Authenticates a user with email and password. Returns a short-lived access token and a refresh token.</div>
        <div class="section-label">Request body</div>
        <table class="params-table">
          <tr><th>Field</th><th>Type</th><th>Required</th><th>Description</th></tr>
          <tr><td class="param-name">email</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>User's email</td></tr>
          <tr><td class="param-name">password</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>User's password</td></tr>
        </table>
        <div class="divider"></div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('login','200',this)">200 OK</div>
          <div class="res-tab" onclick="setTab('login','401',this)">401 Unauthorized</div>
        </div>
        <div id="resp-login-200" class="code-block">{
  "access_token": "eyJhbGci...",
  "refresh_token": "dGhpcyBp...",
  "expires_in": 3600,
  "token_type": "Bearer"
}</div>
        <div id="resp-login-401" class="code-block" style="display:none">{
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Email or password is incorrect."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-login')">Try it out</button>
        <div id="try-login" class="try-panel">
          <div class="try-row">
            <span class="try-method" style="color:#3B6D11">POST</span>
            <input class="try-url" id="url-login" value="https://api.yourservice.com/v1/auth/login" />
            <button class="send-btn" onclick="sendRequest('login','POST')">Send</button>
          </div>
          <div style="margin-bottom:8px">
            <textarea class="try-url" id="body-login" rows="4" style="width:100%;resize:vertical">{
  "email": "ada@example.com",
  "password": "your_password"
}</textarea>
          </div>
          <div class="try-response" id="out-login">// Response will appear here</div>
        </div>
      </div>
    </div>

    <!-- REFRESH -->
    <div id="ep-refresh" class="endpoint-card">
      <div class="endpoint-header" onclick="toggle(this)">
        <span class="badge post">POST</span>
        <span class="endpoint-path">/auth/refresh</span>
        <span class="endpoint-summary">Refresh token</span>
        <span class="chevron">▼</span>
      </div>
      <div class="endpoint-body">
        <div class="desc">Exchanges a valid refresh token for a new access token. Refresh tokens are single-use.</div>
        <div class="section-label">Request body</div>
        <table class="params-table">
          <tr><th>Field</th><th>Type</th><th>Required</th><th>Description</th></tr>
          <tr><td class="param-name">refresh_token</td><td class="param-type">string</td><td><span class="req-badge">required</span></td><td>Valid refresh token</td></tr>
        </table>
        <div class="divider"></div>
        <div class="section-label">Response</div>
        <div class="response-tabs">
          <div class="res-tab tab-2xx" onclick="setTab('refresh','200',this)">200 OK</div>
          <div class="res-tab" onclick="setTab('refresh','401',this)">401 Invalid Token</div>
        </div>
        <div id="resp-refresh-200" class="code-block">{
  "access_token": "eyJhbGci...",
  "expires_in": 3600,
  "token_type": "Bearer"
}</div>
        <div id="resp-refresh-401" class="code-block" style="display:none">{
  "error": {
    "code": "INVALID_TOKEN",
    "message": "Refresh token is expired or invalid."
  }
}</div>
        <button class="open-try-btn" onclick="toggleTry('try-refresh')">Try it out</button>
        <div id="try-refresh" class="try-panel">
          <div class="try-row">
            <span class="try-method" style="color:#3B6D11">POST</span>
            <input class="try-url" id="url-refresh" value="https://api.yourservice.com/v1/auth/refresh" />
            <button class="send-btn" onclick="sendRequest('refresh','POST')">Send</button>
          </div>
          <div style="margin-bottom:8px">
            <textarea class="try-url" id="body-refresh" rows="3" style="width:100%;resize:vertical">{
  "refresh_token": "dGhpcyBp..."
}</textarea>
          </div>
          <div class="try-response" id="out-refresh">// Response will appear here</div>
        </div>
      </div>
    </div>

  </main>
</div>

<script>
function toggle(header) {
  const body = header.nextElementSibling;
  const chevron = header.querySelector('.chevron');
  const isOpen = body.classList.contains('open');
  body.classList.toggle('open', !isOpen);
  body.style.display = isOpen ? 'none' : 'block';
  chevron.classList.toggle('open', !isOpen);
}

function setTab(endpoint, code, el) {
  const container = el.closest('.response-tabs');
  container.querySelectorAll('.res-tab').forEach(t => {
    t.className = 'res-tab';
  });
  const status = parseInt(code);
  if (status >= 200 && status < 300) el.classList.add('tab-2xx');
  else if (status >= 400 && status < 500) el.classList.add('tab-4xx');
  else el.classList.add('tab-5xx');

  const parent = container.parentElement;
  parent.querySelectorAll('[id^="resp-' + endpoint + '"]').forEach(r => r.style.display = 'none');
  const target = document.getElementById('resp-' + endpoint + '-' + code);
  if (target) target.style.display = 'block';
}

function toggleTry(id) {
  const panel = document.getElementById(id);
  panel.classList.toggle('open');
}

function activateNav(el) {
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  el.classList.add('active');
  const targetId = el.getAttribute('href').slice(1);
  const card = document.getElementById(targetId);
  if (card) {
    const body = card.querySelector('.endpoint-body');
    const chevron = card.querySelector('.chevron');
    if (body && !body.classList.contains('open')) {
      body.classList.add('open');
      body.style.display = 'block';
      if (chevron) chevron.classList.add('open');
    }
  }
}

function filterEndpoints(query) {
  const q = query.toLowerCase();
  document.querySelectorAll('.endpoint-card').forEach(card => {
    const text = card.querySelector('.endpoint-path').textContent.toLowerCase()
      + card.querySelector('.endpoint-summary').textContent.toLowerCase()
      + card.querySelector('.badge').textContent.toLowerCase();
    card.style.display = text.includes(q) ? '' : 'none';
  });
}

async function sendRequest(endpoint, method) {
  const urlEl = document.getElementById('url-' + endpoint);
  const outEl = document.getElementById('out-' + endpoint);
  const tokenEl = document.getElementById('token-' + endpoint);
  const bodyEl = document.getElementById('body-' + endpoint);

  if (!urlEl || !outEl) return;
  outEl.textContent = '// Sending request...';

  const headers = { 'Content-Type': 'application/json' };
  if (tokenEl && tokenEl.value.trim()) {
    headers['Authorization'] = 'Bearer ' + tokenEl.value.trim();
  }

  const options = { method, headers };
  if (bodyEl && bodyEl.value.trim() && method !== 'GET') {
    options.body = bodyEl.value.trim();
  }

  try {
    const res = await fetch(urlEl.value, options);
    const text = await res.text();
    let display = '// Status: ' + res.status + ' ' + res.statusText + '\n\n';
    try {
      display += JSON.stringify(JSON.parse(text), null, 2);
    } catch {
      display += text || '(no body)';
    }
    outEl.textContent = display;
  } catch (err) {
    outEl.textContent = '// Error: ' + err.message + '\n// (Check your URL, CORS policy, or network connection)';
  }
}

// Init: show first endpoint open
document.querySelectorAll('.endpoint-body.open').forEach(b => {
  b.style.display = 'block';
});
</script>
</body>
</html>
