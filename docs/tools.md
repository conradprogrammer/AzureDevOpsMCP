# MCP Tools Reference

Complete reference for all Azure DevOps MCP tools.

## Work Item Tools

### devops_get_work_item

Retrieves a work item from Azure DevOps by ID with all details.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workItemId` | int | Yes | Work item ID |

**Example:**
```
devops_get_work_item(workItemId: 25)
```

**Returns:** Work item details including title, state, assigned to, description, acceptance criteria, effort tracking, tags, and relations.

---

### devops_query_work_items

Executes a WIQL query to find work items in Azure DevOps.

**Parameters:**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `wiql` | string | Yes | - | WIQL query string |
| `maxResults` | int | No | 50 | Maximum number of results |

**Example:**
```
devops_query_work_items(
  wiql: "SELECT [System.Id], [System.Title] FROM WorkItems WHERE [System.WorkItemType] = 'User Story' AND [System.State] = 'Active'",
  maxResults: 20
)
```

**WIQL Tips:**
- Use `@project` for current project
- Use `@me` for current user
- Common fields: `System.Id`, `System.Title`, `System.State`, `System.AssignedTo`

---

### devops_get_my_work

Gets all work items assigned to the current user (not closed).

**Parameters:** None

**Example:**
```
devops_get_my_work()
```

**Returns:** Work items grouped by type, sorted by priority.

---

### devops_get_next_task

Gets the next task to work on - either your active task or the highest priority unassigned task.

**Parameters:** None

**Example:**
```
devops_get_next_task()
```

**Returns:** Task details with description and remaining work estimate.

---

### devops_get_epics

Gets all Epics from Azure DevOps, optionally filtered by state.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `state` | string | No | Filter by state (e.g., 'Active', 'New') |

**Example:**
```
devops_get_epics(state: "Active")
```

---

### devops_get_stories

Gets all User Stories from Azure DevOps, optionally filtered by state.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `state` | string | No | Filter by state (e.g., 'Active', 'New', 'Resolved') |

**Example:**
```
devops_get_stories(state: "Active")
```

**Note:** By default excludes Closed stories unless a specific state is provided.

---

### devops_get_tasks

Gets all Tasks from Azure DevOps.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `state` | string | No | Filter by state |

**Example:**
```
devops_get_tasks()
```

---

### devops_create_work_item

Creates a new work item in Azure DevOps.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workItemType` | string | Yes | 'Epic', 'Feature', 'User Story', 'Task', or 'Bug' |
| `title` | string | Yes | Title of the work item |
| `description` | string | No | Description |
| `assignedTo` | string | No | Display name or email |
| `areaPath` | string | No | Area path |
| `iterationPath` | string | No | Iteration path |
| `priority` | int | No | Priority 1-4 |
| `remainingWork` | double | No | Remaining work in hours (for Tasks) |
| `tags` | string | No | Tags (comma-separated) |
| `parentId` | int | No | Parent work item ID |
| `acceptanceCriteria` | string | No | Acceptance criteria (for User Stories) |

**Example:**
```
devops_create_work_item(
  workItemType: "Task",
  title: "Implement login form",
  description: "Create React login component with validation",
  parentId: 20,
  remainingWork: 4.0,
  priority: 2
)
```

---

### devops_update_work_item

Updates a work item's fields in Azure DevOps.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workItemId` | int | Yes | Work item ID |
| `state` | string | No | New state (e.g., 'Active', 'Resolved', 'Closed') |
| `assignedTo` | string | No | Assign to user |
| `title` | string | No | Update title |
| `description` | string | No | Update description |
| `remainingWork` | double | No | Remaining work in hours |
| `completedWork` | double | No | Completed work in hours |
| `priority` | int | No | Priority 1-4 |
| `tags` | string | No | Tags (comma-separated) |
| `comment` | string | No | Add comment to discussion |

**Example:**
```
devops_update_work_item(
  workItemId: 25,
  state: "Active",
  remainingWork: 2.0,
  comment: "Starting work on this task"
)
```

---

### devops_change_state

Changes the state of a work item with optional comment.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workItemId` | int | Yes | Work item ID |
| `state` | string | Yes | New state (e.g., 'Active', 'Resolved', 'Closed') |
| `comment` | string | No | Comment about the state change |

**Example:**
```
devops_change_state(
  workItemId: 25,
  state: "Resolved",
  comment: "Implementation complete, ready for review"
)
```

---

### devops_link_work_items

Creates a link between two work items.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sourceId` | int | Yes | Source work item ID |
| `targetId` | int | Yes | Target work item ID |
| `linkType` | string | Yes | 'Child', 'Parent', 'Related', 'Successor', 'Predecessor' |
| `comment` | string | No | Comment about the link |

**Link Types:**
- `Child` - Creates parent-child hierarchy (source is parent)
- `Parent` - Creates parent-child hierarchy (source is child)
- `Related` - Creates related link
- `Successor` - Creates dependency (source depends on target)
- `Predecessor` - Creates dependency (target depends on source)

**Example:**
```
devops_link_work_items(
  sourceId: 10,
  targetId: 25,
  linkType: "Child",
  comment: "Task for user story"
)
```

---

### devops_unlink_work_items

Removes a link between two work items.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sourceId` | int | Yes | Source work item ID |
| `targetId` | int | Yes | Target work item ID to unlink |

**Example:**
```
devops_unlink_work_items(sourceId: 10, targetId: 25)
```

---

## Git Tools

### devops_link_commit_to_work_item

Links a Git commit to a work item in Azure DevOps.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workItemId` | int | Yes | Work item ID to link to |
| `commitId` | string | Yes | Git commit SHA (full or short) |
| `repositoryName` | string | No | Repository name (defaults to project name) |
| `comment` | string | No | Comment about the link |

**Example:**
```
devops_link_commit_to_work_item(
  workItemId: 25,
  commitId: "a3f5c2d1b9e8f7a6",
  comment: "Implemented feature"
)
```

---

### devops_get_commits

Gets Git commits from Azure DevOps repository.

**Parameters:**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `repositoryName` | string | No | Project name | Repository name |
| `workItemId` | int | No | - | Get commits linked to this work item |
| `author` | string | No | - | Filter by author name or email |
| `top` | int | No | 20 | Number of commits (max: 100) |
| `branch` | string | No | main/master | Branch name |

**Example:**
```
devops_get_commits(
  repositoryName: "MyRepo",
  author: "john@example.com",
  top: 10,
  branch: "develop"
)
```

**Example - Get commits for work item:**
```
devops_get_commits(workItemId: 25)
```

---

### devops_create_pull_request

Creates a pull request in Azure DevOps Git repository.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | PR title |
| `sourceBranch` | string | Yes | Source branch (e.g., 'feature/login') |
| `targetBranch` | string | Yes | Target branch (e.g., 'main') |
| `description` | string | No | PR description |
| `repositoryName` | string | No | Repository name (defaults to project) |
| `workItemIds` | string | No | Work item IDs to link (comma-separated) |
| `reviewers` | string | No | Reviewers (comma-separated emails/names) |

**Example:**
```
devops_create_pull_request(
  title: "Add user authentication",
  sourceBranch: "feature/auth",
  targetBranch: "main",
  description: "Implements OAuth2 authentication flow",
  workItemIds: "25,26,27",
  reviewers: "john@example.com,jane@example.com"
)
```

---

### devops_get_pull_request_status

Gets pull request status, reviewers, and review votes.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `pullRequestId` | int | Yes | Pull request ID |
| `repositoryName` | string | No | Repository name (defaults to project) |

**Example:**
```
devops_get_pull_request_status(pullRequestId: 42)
```

**Returns:** PR details including status, source/target branches, reviewers with votes:
- `Approved` (10)
- `Approved with suggestions` (5)
- `No vote` (0)
- `Waiting for author` (-5)
- `Rejected` (-10)

---

## Pipeline Tools

### devops_trigger_pipeline

Triggers a pipeline (build) in Azure DevOps.

**Parameters:**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `pipeline` | string | Yes | - | Pipeline name or ID |
| `branch` | string | No | main | Branch name |
| `parameters` | string | No | - | Parameters as 'key1=value1,key2=value2' |

**Example:**
```
devops_trigger_pipeline(
  pipeline: "CI-Build",
  branch: "develop",
  parameters: "configuration=Release,runTests=true"
)
```

---

### devops_get_pipeline_status

Gets the status of a pipeline build.

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `buildId` | int | Yes | Build ID |

**Example:**
```
devops_get_pipeline_status(buildId: 123)
```

**Returns:** Build status, result, branch, requester, timing, and duration.

---

### devops_get_build_logs

Gets build logs to diagnose failures.

**Parameters:**
| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `buildId` | int | Yes | - | Build ID |
| `lines` | int | No | 100 | Number of log lines (max: 500) |

**Example:**
```
devops_get_build_logs(buildId: 123, lines: 200)
```

**Returns:** Build logs organized by log type with formatted output.

---

## Common Patterns

### Starting Work on a Task
```
1. devops_get_next_task()
2. devops_change_state(workItemId: X, state: "Active")
```

### Completing a Task
```
1. devops_change_state(workItemId: X, state: "Resolved", comment: "Done")
2. devops_link_commit_to_work_item(workItemId: X, commitId: "abc123")
3. devops_create_pull_request(title: "...", workItemIds: "X")
```

### Creating a User Story with Tasks
```
1. devops_create_work_item(workItemType: "User Story", title: "...")
2. devops_create_work_item(workItemType: "Task", title: "...", parentId: storyId)
```

### Checking Build Status
```
1. devops_trigger_pipeline(pipeline: "CI-Build")
2. devops_get_pipeline_status(buildId: X)
3. devops_get_build_logs(buildId: X)  // if failed
```

