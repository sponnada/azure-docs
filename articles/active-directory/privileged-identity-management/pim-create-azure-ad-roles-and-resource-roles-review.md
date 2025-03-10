---
title: Create an access review of Azure resource and Azure AD roles in PIM - Azure AD | Microsoft Docs
description: Learn how to create an access review of Azure resource and Azure AD roles in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
---

# Create an access review of Azure resource and Azure AD roles in PIM

The need for access to privileged Azure resource and Azure AD roles by employees changes over time. To reduce the risk associated with stale role assignments, you should regularly review access. You can use Azure Active Directory (Azure AD) Privileged Identity Management (PIM) to create access reviews for privileged access to Azure resource and Azure AD roles. You can also configure recurring access reviews that occur automatically. This article describes how to create one or more access reviews.

## Prerequisites

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] For more information about licenses for PIM, refer to [License requirements to use Privileged Identity Management](subscription-requirements.md).

 To create access reviews for Azure resources, you must be assigned to the [Owner](../../role-based-access-control/built-in-roles.md#owner) or the [User Access Administrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) role for the Azure resources. To create access reviews for Azure AD roles, you must be assigned to the [Global Administrator](../roles/permissions-reference.md#global-administrator) or the [Privileged Role Administrator](../roles/permissions-reference.md#privileged-role-administrator) role.

> [!Note]
> In public preview, you can scope an access review to service principals with access to Azure AD and Azure resource roles with an Azure Active Directory Premium P2 edition active in your tenant. After general availability, additional licenses might be required.

## Create access reviews

1. Sign in to [Azure portal](https://portal.azure.com/) as a user that is assigned to one of the prerequisite role(s).

2. Select **Identity Governance**.
 
3. For **Azure AD roles**, select **Azure AD roles** under **Privileged Identity Management**. For **Azure resources**, select **Azure resources** under **Privileged Identity Management**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="Select Identity Governance in Azure Portal screenshot." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. For **Azure AD roles**, select **Azure AD roles** again under **Manage**. For **Azure resources**, select the subscription you want to manage.


5. Under Manage, select **Access reviews**, and then select **New** to create a new access review.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Azure AD roles - Access reviews list showing the status of all reviews screenshot.":::
 
6. Name the access review. Optionally, give the review a description. The name and description are shown to the reviewers.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="Create an access review - Review name and description screenshot.":::

7. Set the **Start date**. By default, an access review occurs once, starts the same time it's created, and it ends in one month. You can change the start and end dates to have an access review start in the future and last however many days you want.

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text="Start date, frequency, duration, end, number of times, and end date screenshot.":::

8. To make the access review recurring, change the **Frequency** setting from **One time** to **Weekly**, **Monthly**, **Quarterly**, **Annually**, or **Semi-annually**. Use the **Duration** slider or text box to define how many days each review of the recurring series will be open for input from reviewers. For example, the maximum duration that you can set for a monthly review is 27 days, to avoid overlapping reviews.

9. Use the **End** setting to specify how to end the recurring access review series. The series can end in three ways: it runs continuously to start reviews indefinitely, until a specific date, or after a defined number of occurrences has been completed. You, or another administrator who can manage reviews, can stop the series after creation by changing the date in **Settings**, so that it ends on that date.


10. In the **Users Scope** section, select the scope of the review. For **Azure AD roles**, the first scope option is Users and Groups. Directly assigned users and [role-assignable groups](../roles/groups-concept.md) will be included in this selection. For **Azure resource roles**, the first scope will be Users. Groups assigned to Azure resource roles are expanded to display transitive user assignments in the review with this selection. You may also select **Service Principals** to review the machine accounts with direct access to either the Azure resource or Azure AD role.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text="Users scope to review role membership of screenshot.":::

11. Under **Review role membership**, select the privileged Azure resource or Azure AD roles to review.

    > [!NOTE]
    > Selecting more than one role will create multiple access reviews. For example, selecting five roles will create five separate access reviews.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="Review role memberships screenshot.":::

12. In **assignment type**, scope the review by how the principal was assigned to the role. Choose **eligible assignments only** to review eligible assignments (regardless of activation status when the review is created) or **active assignments only** to review active assignments. Choose **all active and eligible assignments** to review all assignments regardless of type.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="Reviewers list of assignment types screenshot.":::

13. In the **Reviewers** section, select one or more people to review all the users. Or you can select to have the members review their own access.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="Reviewers list of selected users or members (self)":::

    - **Selected users** - Use this option to designate a specific user to complete the review. This option is available regardless of the scope of the review, and the selected reviewers can review users, groups and service principals.
    - **Members (self)** - Use this option to have the users review their own role assignments. This option is only available if the review is scoped to **Users and Groups** or **Users**. For **Azure AD roles**, role-assignable groups will not be a part of the review when this option is selected. 
    - **Manager** – Use this option to have the user’s manager review their role assignment. This option is only available if the review is scoped to **Users and Groups** or **Users**. Upon selecting Manager, you will also have the option to specify a fallback reviewer. Fallback reviewers are asked to review a user when the user has no manager specified in the directory. For **Azure AD roles**, role-assignable groups will be reviewed by the fallback reviewer if one is selected. 

### Upon completion settings

1. To specify what happens after a review completes, expand the **Upon completion settings** section.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="Upon completion settings to auto apply and should review not respond screenshot.":::

2. If you want to automatically remove access for users that were denied, set **Auto apply results to resource** to **Enable**. If you want to manually apply the results when the review completes, set the switch to **Disable**.

3. Use the **If reviewer don't respond** list to specify what happens for users that are not reviewed by the reviewer within the review period. This setting does not impact users who were reviewed by the reviewers.

    - **No change** - Leave user's access unchanged
    - **Remove access** - Remove user's access
    - **Approve access** - Approve user's access
    - **Take recommendations** - Take the system's recommendation on denying or approving the user's continued access
    
4. Use the **Action to apply on denied guest users** list to specify what happens for guest users that are denied. This setting is not editable for Azure AD and Azure resource role reviews at this time; guest users, like all users, will always lose access to the resource if denied.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="Upon completion settings - Action to apply on denied guest users screenshot.":::

5. You can send notifications to additional users or groups to receive review completion updates. This feature allows for stakeholders other than the review creator to be updated on the progress of the review. To use this feature, select **Select User(s) or Group(s)** and add an additional user or group upon you want to receive the status of completion.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="Upon completion settings - Add additional users to receive notifications screenshot.":::

### Advanced settings

1. To specify additional settings, expand the **Advanced settings** section.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="Advanced settings for show recommendations, require reason on approval, mail notifications, and reminders screenshot.":::

1. Set **Show recommendations** to **Enable** to show the reviewers the system recommendations based the user's access information.

1. Set **Require reason on approval** to **Enable** to require the reviewer to supply a reason for approval.

1. Set **Mail notifications** to **Enable** to have Azure AD send email notifications to reviewers when an access review starts, and to administrators when a review completes.

1. Set **Reminders** to **Enable** to have Azure AD send reminders of access reviews in progress to reviewers who have not completed their review.
1. The content of the email sent to reviewers is auto-generated based on the review details, such as review name, resource name, due date, etc. If you need a way to communicate additional information such as additional instructions or contact information, you can specify these details in the **Additional content for reviewer email** which will be included in the invitation and reminder emails sent to assigned reviewers. The highlighted section below is where this information will be displayed.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="Content of the email sent to reviewers with highlights"::: 

## Manage the access review

You can track the progress as the reviewers complete their reviews on the **Overview** page of the access review. No access rights are changed in the directory until the review is completed. Below is a screenshot showing the overview page for **Azure resources** and **Azure AD roles** access reviews.

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="Access reviews overview page showing the details of the access review for Azure AD roles screenshot." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

If this is a one-time review, then after the access review period is over or the administrator stops the access review, follow the steps in [Complete an access review of Azure resource and Azure AD roles](pim-complete-azure-ad-roles-and-resource-roles-review.md) to see and apply the results.  

To manage a series of access reviews, navigate to the access review, and you will find upcoming occurrences in Scheduled reviews, and edit the end date or add/remove reviewers accordingly.

Based on your selections in **Upon completion settings**, auto-apply will be executed after the review's end date or when you manually stop the review. The status of the review will change from **Completed** through intermediate states such as **Applying** and finally to state **Applied**. You should expect to see denied users, if any, being removed from roles in a few minutes.

> [!IMPORTANT]
> If a group is assigned to **Azure resource roles**, the reviewer of the Azure resource role will see the expanded list of the indirect users with access assigned through a nested group. Should a reviewer deny a member of a nested group, that deny result will not be applied successfully for the role because the user will not be removed from the nested group. For **Azure AD roles**, [role-assignable groups](../roles/groups-concept.md) will show up in the review instead of expanding the members of the group, and a reviewer will either approve or deny access to the entire group.

## Update the access review

After one or more access reviews have been started, you may want to modify or update the settings of your existing access reviews. Here are some common scenarios that you might want to consider:

- **Adding and removing reviewers** - When updating access reviews, you may choose to add a fallback reviewer in addition to the primary reviewer. Primary reviewers may be removed when updating an access review. However, fallback reviewers are not removable by design.

    > [!Note]
    > Fallback reviewers can only be added when reviewer type is manager. Primary reviewers can be added when reviewer type is selected user.

- **Reminding the reviewers** - When updating access reviews, you may choose to enable the reminder option under Advanced Settings. Once enabled, users will receive an email notification at the midpoint of the review period, regardless of whether they have completed the review or not. 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="Screenshot of the reminder option under access reviews settings.":::

- **Updating the settings** - If an access review is recurring, there are separate settings under "Current" versus under "Series". Updating the settings under "Current" will only apply changes to the current access review while updating the settings under "Series" will update the setting for all future recurrences.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="Screenshot of the settings page under access reviews." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## Next steps

- [Perform an access review of Azure resource and Azure AD roles in PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [Complete an access review of Azure resource and Azure AD roles in PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
