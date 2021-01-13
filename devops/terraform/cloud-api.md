### Applies API

An apply represents the results of applying a Terraform Run's execution plan.

| State                     | Description                                                  |
| ------------------------- | ------------------------------------------------------------ |
| `pending`                 | The initial status of a apply once it has been created.      |
| `managed_queued`/`queued` | The apply has been queued, awaiting backend service capacity to run terraform. |
| `running`                 | The apply is running.                                        |
| `errored`                 | The apply has errored. This is a final state.                |
| `canceled`                | The apply has been canceled. This is a final state.          |
| `finished`                | The apply has completed sucessfully. This is a final state.  |
| `unreachable`             | The apply will not run. This is a final state.               |

###### Show an apply
```
GET /applies/:id
```



### Notification Configurations API

Terraform Cloud can be configured to send notifications for run state  transitions. The configuration allows you to specify a destination URL,  request type, and what events will trigger the notification. Each  workspace can have up to 20 notification configurations, and they apply  to all runs for that workspace.

Notification Triggers:

| Display Name    | Value                   | Description                                                  |
| --------------- | ----------------------- | ------------------------------------------------------------ |
| Created         | `"run:created"`         | When a run is created and enters the "Pending" state.        |
| Planning        | `"run:planning"`        | When a run acquires the lock and starts to execute.          |
| Needs Attention | `"run:needs_attention"` | Human decision required. When a plan has changes and is not auto-applied, or requires a policy override. |
| Applying        | `"run:applying"`        | When a run begins the apply stage, after a plan is confirmed or auto-applied. |
| Completed       | `"run:completed"`       | When the run has completed on a happy path and can't go any further. |
| Errored         | `"run:errored"`         | When the run has terminated early due to error or cancelation. |



### Plans API

A plan represents the execution plan of a Run in a Terraform workspace.

Plan States:

| State                     | Description                                                  |
| ------------------------- | ------------------------------------------------------------ |
| `pending`                 | The initial status of a plan once it has been created.       |
| `managed_queued`/`queued` | The plan has been queued, awaiting backend service capacity to run terraform. |
| `running`                 | The plan is running.                                         |
| `errored`                 | The plan has errored. This is a final state.                 |
| `canceled`                | The plan has been canceled. This is a final state.           |
| `finished`                | The plan has completed sucessfully. This is a final state.   |
| `unreachable`             | The plan will not run. This is a final state.                |

###### Show a plan
```
GET /plans/:id
```

###### Retrieve the JSON execution plan
```
GET /plans/:id/json-output
GET /runs/:id/plan/json-output
```



### Runs API

A run performs a plan and apply, using a configuration version and the workspace’s current variables. 

Run States:

| State                  | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `pending`              | The initial status of a run once it has been created.        |
| `plan_queued`          | Once a workspace has the availability to start a new run, the next run will transition to `plan_queued`. This status indicates that the it should start as soon as the backend  services that run terraform have available capacity.  In Terraform  Cloud, you should seldom see this status, as our aim is to always have  capacity. However, in Terraform Enterprise this status will be more  common due to the self-hosted nature. |
| `planning`             | The planning phase of a run is in progress.                  |
| `planned`              | The planning phase of a run has completed.                   |
| `cost_estimating`      | The cost estimation phase of a run is in progress.           |
| `cost_estimated`       | The cost estimation phase of a run has completed.            |
| `policy_checking`      | The sentinel policy checking phase of a run is in progress.  |
| `policy_override`      | A sentinel policy has soft failed, and can be overriden.     |
| `policy_soft_failed`   | A sentinel policy has soft failed for a plan-only run.  This is a final state. |
| `policy_checked`       | The sentinel policy checking phase of a run has completed.   |
| `confirmed`            | The plan produced by the run has been confirmed.             |
| `planned_and_finished` | The completion of a run containing a plan only, or a run the produces a plan with no changes to apply.  This is a final state. |
| `apply_queued`         | Once the changes in the plan have been confirmed, the run run will transition to `apply_queued`. This status indicates that the run should start as soon as the backend  services that run terraform have available capacity. In Terraform Cloud, you should seldom see this status, as our aim is to always have  capacity. However, in Terraform Enterprise this status will be more  common due to the self-hosted nature. |
| `applying`             | The applying phase of a run is in progress.                  |
| `applied`              | The applying phase of a run has completed.                   |
| `discarded`            | The run has been discarded. This is a final state.           |
| `errored`              | The run has errored. This is a final state.                  |
| `canceled`             | The run has been canceled.                                   |
| `force_canceled`       | The run has been canceled forcefully.                        |

###### Create a Run
```
POST /runs
```

###### Apply a Run
```
POST /runs/:run_id/actions/apply
```

###### List Runs in a Workspace
```
GET /workspaces/:workspace_id/runs
```

###### Get run details
```
GET /runs/:run_id
```

###### Discard a Run
```
POST /runs/:run_id/actions/discard
```

###### Cancel a Run
```
POST /runs/:run_id/actions/cancel
```

###### Forcefully cancel a run
```
POST /runs/:run_id/actions/force-cancel
```

###### Forcefully execute a run
```
POST /runs/:run_id/actions/force-execute
```



### Workspaces API

Workspaces represent running infrastructure managed by Terraform.

###### Create a Workspace
```
POST /organizations/:organization_name/workspaces
```

###### Update a Workspace
```
PATCH /workspaces/:workspace_id
```

###### List workspaces
```
GET /organizations/:organization_name/workspaces
```

###### Show workspace
```
GET /workspaces/:workspace_id
```

###### Delete a workspace
```
DELETE /workspaces/:workspace_id
```

###### Lock a workspace
```
POST /workspaces/:workspace_id/actions/lock
```

###### Unlock a workspace
```
POST /workspaces/:workspace_id/actions/unlock
```

###### Force Unlock a workspace
```
POST /workspaces/:workspace_id/actions/force-unlock
```

###### Assign an SSH key to a workspace
```
PATCH /workspaces/:workspace_id/relationships/ssh-key
```

###### Unassign an SSH key from a workspace
```
PATCH /workspaces/:workspace_id/relationships/ssh-key
```

