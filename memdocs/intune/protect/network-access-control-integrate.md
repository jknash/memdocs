---
# required metadata

title: Network access control integration with Microsoft Intune
description: Network access control (NAC) solutions check enrollment and compliance for devices with Intune. NAC includes certain behaviors and works with Conditional Access. See the steps to get onboarded, and get a list of partner solutions.
keywords:
author: brenduns
ms.author: brenduns
manager: dougeby
ms.date: 06/01/2021
ms.topic: how-to
ms.service: microsoft-intune
ms.subservice: protect
ms.localizationpriority: high
ms.technology:
ms.assetid: aa7ecff7-8579-4009-8fd6-e17074df67de

# optional metadata

#ROBOTS:
#audience:

ms.reviewer: tycast
ms.suite: ems
search.appverid: MET150
#ms.tgt_pltfrm:
ms.custom: intune-azure
ms.collection: M365-identity-device-management
---

# Network access control (NAC) integration with Intune
 
Intune integrates with network access control (NAC) partners to help organizations secure corporate data when devices try to access on-premises resources.

>[!IMPORTANT]
> NAC is not currently supported for Android Enterprise Fully Managed or Android Enterprise Dedicated devices.

## How do Intune and NAC solutions help protect your organization resources?

NAC solutions check the device enrollment and compliance state with Intune to make access control decisions. If the device isn't enrolled, or is enrolled and not compliant with Intune device compliance policies, then the device should be redirected to Intune for enrollment, or for a device compliance check.

### Example

If the device is enrolled and compliant with Intune, the NAC solution should allow the device access to corporate resources. For example, users can be allowed or denied access when trying to access corporate Wi-Fi or VPN resources.

## Feature behaviors

Devices that are actively syncing to Intune can't move from **Compliant** / **Noncompliant** to **Not Synced** (or **Unknown**). The **Unknown** state is reserved for newly enrolled devices that haven't been evaluated for compliance yet.

For devices that are blocked from access to resources, the blocking service should redirect all users to the [management portal](https://portal.manage.microsoft.com) to determine why the device is blocked.  If the users visit this page, their devices are synchronously reevaluated for compliance.

## NAC and Conditional Access

NAC works with Conditional Access to provide access control decisions. For more information, see [Common ways to use Conditional Access with Intune](conditional-access-intune-common-ways-use.md).

## How the NAC integration works

The following list is an overview on how NAC integration works when integrated with Intune. The first three steps, 1-3, explain the onboarding process. Once the NAC solution is integrated with Intune, steps 4-9 describe the ongoing operation.

![Conceptual image of how NAC works with Intune](./media/network-access-control-integrate/ca-intune-common-ways-2.png)

1. Register the NAC partner solution with Azure Active Directory (AAD), and grant delegated permissions to the Intune NAC API.
2. Configure the NAC partner solution with the appropriate settings including the Intune discovery URL.
3. Configure the NAC partner solution for certificate authentication.
4. User connects to corporate Wi-Fi access point or makes a VPN connection request.
5. NAC partner solution forwards the device information to Intune, and asks Intune about the device enrollment and compliance state.
6. If the device isn't compliant or isn't enrolled, the NAC partner solution instructs the user to enroll or fix the device compliance.
7. The device tries to reverify its compliance and enrollment state when applicable.
8. Once the device is enrolled and compliant, NAC partner solution gets the state from Intune.
9. Connection is successfully established which allows the device access to corporate resources.

> [!NOTE] 
> NAC partner solutions will typically make two different types of query to Intune to ask about device compliance state:
> 
> - Queries filtering based on a known property value of a single device such as its IMEI or Wi-Fi MAC address
> - Broad, unfiltered queries for all non-compliant devices.
>
> NAC Solutions are permitted to make as many of the device-specific queries as required.  However the broad unfiltered queries may be throttled. The NAC solution should be configured to only submit the *all non-compliant devices* queries, at most, once every four hours. Queries made more frequently will receive an http 503 error from the Intune service.

## Use NAC for VPN on your iOS/iPadOS devices

NAC is available on the following VPNs without enabling NAC in the VPN profile:

- NAC for Cisco Legacy AnyConnect
- F5 Access Legacy
- Citrix VPN

NAC is also supported for Cisco AnyConnect, Citrix SSO, and F5 Access.

### To enable NAC for Cisco AnyConnect for iOS

- Integrate ISE with Intune for NAC as described in the link below.
- Set the **Enable Network Access Control (NAC)** setting in the VPN profile to **Yes**.

### To enable NAC for Citrix SSO

- Use Citrix Gateway 12.0.59 or higher.  
- Users must have Citrix SSO 1.1.6 or later installed.
- [Integrate NetScaler with Intune for NAC](https://docs.citrix.com/en-us/citrix-gateway/current-release/microsoft-intune-integration/configuring-network-access-control-device-check-for-citrix-gateway-virtual-server-for-single-factor-authentication-deployment.html) as described in the Citrix product documentation.
- In the VPN profile, select **Base settings** > **Enable Network Access Control (NAC)** > select **I agree**.

### To enable NAC for F5 Access

- Use F5 BIG-IP 13.1.1.5 or later.
- Integrate BIG-IP with Intune for NAC. The [Overview: Configuring APM for device posture checks with endpoint management systems](https://support.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-client-configuration-7-1-6/6.html#guid-0bd12e12-8107-40ec-979d-c44779a8cc89) F5 guide lists the steps.
- In the VPN profile, select **Base settings** > **Enable Network Access Control (NAC)** > select **I agree**.

The VPN connection is disconnected every 24 hours for security reasons. The VPN can immediately be reconnected.

We're working with our partners to release a NAC solution for these newer clients. When solutions are ready, this article will be updated with additional information.

## Next steps

- [Integrate Cisco ISE with Intune](https://www.cisco.com/c/en/us/td/docs/security/ise/2-1/admin_guide/b_ise_admin_guide_21/b_ise_admin_guide_20_chapter_01000.html)
- [Integrate Citrix NetScaler with Intune](https://docs.citrix.com/en-us/citrix-gateway/current-release/microsoft-intune-integration/configuring-network-access-control-device-check-for-citrix-gateway-virtual-server-for-single-factor-authentication-deployment.html)
- [Integrate F5 BIG-IP Access Policy Manager with Intune](https://support.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-client-configuration-13-0-0/6.html)
- [Integrate HP Aruba ClearPass with Intune](https://support.arubanetworks.com/Documentation/tabid/77/DMXModule/512/Command/Core_Download/Default.aspx?EntryId=31271)
- [Integrate Squadra security Removable Media Manager (secRMM) with Intune](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMIntuneAccessControlSetupGuide.pdf)
