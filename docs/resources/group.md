---
page_title: "opal_group Resource - terraform-provider-opal"
subcategory: ""
description: |-
  An Opal Group resource.
---

# opal_group (Resource)

An Opal Group resource.

## Example Usage

```terraform
resource "opal_group" "opal_group_example" {
  name = "Opal group"
  description = "Opal group created via terraform"
  group_type = "OPAL_GROUP"
  app_id = data.opal_app.opal.id
  admin_owner_id = opal_owner.security.id
  require_mfa_to_approve = true
  auto_approval = false

  reviewer_stage {
    reviewer {
      id = opal_owner.security.id
    }
  }
}
```

## Reviewer Configuration
Reviewer configuration can be managed via the `is_requestable`, `auto_approval`, and `reviewer_stage` attributes. See examples below:

```terraform
resource "opal_group" "not_requestable" {
  // ...

  // If you want a group to not be requestable, you can set `is_requestable` to false and omit the `reviewer_stage` attribute
  is_requestable = false
}

resource "opal_group" "auto_approval" {
  // ...

  // If you want a group to be auto-approved, you can set `auto_approval` to true and omit the `reviewer_stage` attribute
  auto_approval = false
}

resource "opal_group" "basic_reviewer_config" {
  // ...

  // NOTE: operator = "AND" and require_manager_approval = false are the default if not explicitly set
  reviewer_stage {
    reviewer {
      id = opal_owner.security.id
    }
  }
}

resource "opal_group" "or_reviewer_config" {
  // ...

  // Here the manager of the requesting user or the security owner would need to approve
  reviewer_stage {
    operator = "OR"
    require_manager_approval = true
    reviewer {
      id = opal_owner.security.id
    }
  }
}

resource "opal_group" "complex_reviewer_config" {
  // ...

  // Here first the manager has to approve. Once the manager has approved, both the security owner and the data owner need to approve
  // NOTE: The ordering determines the ordering of the stages
  reviewer_stage {
    require_manager_approval = true
  }

  reviewer_stage {
    reviewer {
      id = opal_owner.security.id
    }

    reviewer {
      id = opal_owner.data.id
    }
  }
}
```

## Remote Groups
Remote groups can be managed using the `remote_info` attribute. See the examples below:

```terraform
resource "opal_group" "okta_group_example" {
  name = "Okta group"
  description = "Okta group created via terraform"
  group_type = "OKTA_GROUP"
  app_id = data.opal_app.okta.id
  admin_owner_id = opal_owner.security.id

  remote_info {
    okta_group {
      # Note: This can reference your Okta terraform files
      group_id = "00gd7wmwj7hfT3wTH8d6"
    }
  }
}

resource "opal_group" "google_group_example" {
  name = "Google group"
  // ...

  remote_info {
    google_group {
      # Note: This can reference your Google terraform files
      group_id = "081mfhsl1l8rxqv"
    }
  }
}
```


<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `admin_owner_id` (String) The admin owner ID for this group. By default, this is set to the application admin owner.
- `app_id` (String) The ID of the app integration that provides the group. You can get this value from the URL of the app in the Opal web app. For an Opal group, use the ID from the Opal app in the apps view.
- `group_type` (String) The type of the group, i.e. GIT_HUB_TEAM.
- `name` (String) The name of the group.

### Optional

- `audit_message_channel` (Block Set) An audit message channel for this group. (see [below for nested schema](#nestedblock--audit_message_channel))
- `auto_approval` (Boolean) Automatically approve all requests for this group without review.
- `description` (String) The description of the group.
- `is_requestable` (Boolean) Allow users to create an access request for this group. By default, any group is requestable.
- `manage_resources` (Boolean) Boolean flag to indicate if you intend to manage group <-> resource relationships via terraform.
- `max_duration` (Number) The maximum duration for which this group can be requested (in minutes).
- `on_call_schedule` (Block Set) An on call schedule for this group. (see [below for nested schema](#nestedblock--on_call_schedule))
- `recommended_duration` (Number) The recommended duration for which the group should be requested (in minutes). Will be the default value in a request. Use -1 to set to indefinite.
- `remote_info` (Block List, Max: 1) Remote info that is required for the creation of remote groups. (see [below for nested schema](#nestedblock--remote_info))
- `request_template_id` (String) The ID of a request template for this group. You can get this ID from the URL in the Opal web app.
- `require_mfa_to_approve` (Boolean) Require that reviewers MFA to approve requests for this group.
- `require_mfa_to_request` (Boolean) Require that users MFA to request this group.
- `require_support_ticket` (Boolean) Require that requesters attach a support ticket to requests for this group.
- `resource` (Block Set) A resource that members of the group get access to. (see [below for nested schema](#nestedblock--resource))
- `reviewer_stage` (Block List) A reviewer stage for this group. You are allowed to provide up to 3. (see [below for nested schema](#nestedblock--reviewer_stage))
- `visibility` (String) The visibility level of the group, i.e. LIMITED or GLOBAL.
- `visibility_group` (Block List) The groups that can see this group when visibility is limited. If not specified, only users with direct access can see this resource when visibility is set to LIMITED. (see [below for nested schema](#nestedblock--visibility_group))

### Read-Only

- `id` (String) The ID of the group.

<a id="nestedblock--audit_message_channel"></a>
### Nested Schema for `audit_message_channel`

Required:

- `id` (String) The ID of the message channel for this group.


<a id="nestedblock--on_call_schedule"></a>
### Nested Schema for `on_call_schedule`

Required:

- `id` (String) The UUID of the on call schedule for this group.


<a id="nestedblock--remote_info"></a>
### Nested Schema for `remote_info`

Optional:

- `active_directory_group` (Block List, Max: 1) The remote_info for an Active Directory group. (see [below for nested schema](#nestedblock--remote_info--active_directory_group))
- `duo_group` (Block List, Max: 1) The remote_info for an Duo Security group. (see [below for nested schema](#nestedblock--remote_info--duo_group))
- `github_team` (Block List, Max: 1) The remote_info for a GitHub team. (see [below for nested schema](#nestedblock--remote_info--github_team))
- `gitlab_group` (Block List, Max: 1) The remote_info for a Gitlab group. (see [below for nested schema](#nestedblock--remote_info--gitlab_group))
- `google_group` (Block List, Max: 1) The remote_info for a Google group. (see [below for nested schema](#nestedblock--remote_info--google_group))
- `ldap_group` (Block List, Max: 1) The remote_info for a LDAP group. (see [below for nested schema](#nestedblock--remote_info--ldap_group))
- `okta_group` (Block List, Max: 1) The remote_info for an Okta group. (see [below for nested schema](#nestedblock--remote_info--okta_group))

<a id="nestedblock--remote_info--active_directory_group"></a>
### Nested Schema for `remote_info.active_directory_group`

Required:

- `group_id` (String) The id of the Active Directory group.


<a id="nestedblock--remote_info--duo_group"></a>
### Nested Schema for `remote_info.duo_group`

Required:

- `group_id` (String) The id of the Duo Security group.


<a id="nestedblock--remote_info--github_team"></a>
### Nested Schema for `remote_info.github_team`

Required:

- `team_slug` (String) The slug of the GitHub team.


<a id="nestedblock--remote_info--gitlab_group"></a>
### Nested Schema for `remote_info.gitlab_group`

Required:

- `group_id` (String) The id of the Gitlab group.


<a id="nestedblock--remote_info--google_group"></a>
### Nested Schema for `remote_info.google_group`

Required:

- `group_id` (String) The id of the Google group.


<a id="nestedblock--remote_info--ldap_group"></a>
### Nested Schema for `remote_info.ldap_group`

Required:

- `group_id` (String) The id of the LDAP group.


<a id="nestedblock--remote_info--okta_group"></a>
### Nested Schema for `remote_info.okta_group`

Required:

- `group_id` (String) The id of the Okta group.



<a id="nestedblock--resource"></a>
### Nested Schema for `resource`

Required:

- `id` (String) The ID of the resource.

Optional:

- `access_level_remote_id` (String) The access level remote id of the resource that this group gives access to.


<a id="nestedblock--reviewer_stage"></a>
### Nested Schema for `reviewer_stage`

Optional:

- `operator` (String) The operator of the stage. Operator is either "AND" or "OR".
- `require_manager_approval` (Boolean) Whether this reviewer stage should require manager approval.
- `reviewer` (Block Set) A reviewer for this stage. (see [below for nested schema](#nestedblock--reviewer_stage--reviewer))

<a id="nestedblock--reviewer_stage--reviewer"></a>
### Nested Schema for `reviewer_stage.reviewer`

Required:

- `id` (String) The ID of the owner.



<a id="nestedblock--visibility_group"></a>
### Nested Schema for `visibility_group`

Required:

- `id` (String) The ID of the group that can see this group.

Please [file a ticket](https://github.com/opalsecurity/terraform-provider-opal/issues) to discuss use cases that are not yet supported in the provider.
