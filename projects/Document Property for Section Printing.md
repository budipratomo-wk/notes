---
title: projects/Document Property for Section Printing
date:  2023-01-19
---
#projects

Tip: afterwards, formally ask them for feedback!
use this as a promotion opportunity!

# References 
[Feature Brief](https://sandbox.wdesk.com/a/QWNjb3VudB8yMDAx/doc/03d35051fa67469ebe92a143686b093b/r/-1/v/1/sec/03d35051fa67469ebe92a143686b093b_16)
[Gestures](https://github.com/Workiva/skaardb/blob/master/cerberus/internal/backend/gesture/README.md)

# What we're building:
An additional toggle in the doc properties to mark as non-printing. 
They want the doc properties 


# Keywords
documentMetadataIndex => Struct in go in skaardb
skaardb gestures
openDocResponse
analytics on clicks
Access Control Module (ACM) => in dpc
MinimumPermission
activity_evalutation.dart
figure out whether spreadsheets would need non-printing
messages.dart
audit_log => audit_frugal

# Code sections of interests:
- [[repository_notes/skaardb/gesture]]
- [[repository_notes/skaardb/common-documentmeta]]

## Reference PRs
- DOCPLAT-13033 Add MinApproveTrackChanges to document properties response and add operation 
    https://github.com/Workiva/skaardb/pull/30682

- DOCPLAT-13094 Set and get minApproveTrackChangesPerm on the document properties
https://github.com/Workiva/skaardb/pull/30817

- DOCPLAT-13236 Check approve track changes permissions based on MinApproveTrackChangesPerm
https://github.com/Workiva/skaardb/pull/30964

- DOCPLAT-13220 Add MinApproveTrackChangesPerm to OpenDocResponse
https://github.com/Workiva/skaardb/pull/31081

- OM-11151 Add new audit event for tack changes
https://github.com/Workiva/admin_frugal/pull/121

- DOCPLAT-13462 Change ReviewCreatorPerm to MinimumPermission
https://github.com/Workiva/doc_plat_client/pull/21501

- DOCPLAT-13482 - Remove usages of enableCanSendForReview flag
https://github.com/Workiva/doc_plat_client/pull/21513

- DOCPLAT-13487 Add approve track changes dropdown behind flag
https://github.com/Workiva/doc_plat_client/pull/21551

- DOCPLAT-13558 Add audit event when minApproveTrackChangesPerm is updated
https://github.com/Workiva/skaardb/pull/31732

- DOCPLAT-13607 Use minApproveTrackChangesPerm for _evaluateAcceptTrackChangesForSection
https://github.com/Workiva/doc_plat_client/pull/21855

- DOCPLAT-13671 Only set approve track changes and review perms on doc props when they are actually updated
https://github.com/Workiva/doc_plat_client/pull/21938

- DOCPLAT-13710 Add value after string to attachments for updating min permissions
https://github.com/Workiva/skaardb/pull/31976

- DOCPLAT-13711 Add tooltips to review toolbar for track changes
https://github.com/Workiva/doc_plat_client/pull/21972

- DOCPLAT-13729 Change reject all section changes to require editor permission
https://github.com/Workiva/skaardb/pull/32006

- DOCPLAT-13781 Approve/Reject track changes context menu tooltips
https://github.com/Workiva/doc_plat_client/pull/22113

- DOCPLAT-13811 Track changes card tooltips
https://github.com/Workiva/doc_plat_client/pull/22162

- DOCPLAT-13821 Analytics for min approve track changes permission setting
https://github.com/Workiva/doc_plat_client/pull/22168

- DOCPLAT-13846 Update review toolbar track changes tooltips
https://github.com/Workiva/doc_plat_client/pull/22208

- DOCPLAT-13880 Blitz updates - Remove for presentations and show permissions menu item for all entitlements
https://github.com/Workiva/doc_plat_client/pull/22257

- DOCPLAT-14411 Cleanup Track Changes LD flag
https://github.com/Workiva/doc_plat_client/pull/23557


