While some XBRL modal tabs (e.g. Defaults) commit changes directly to memory, some tabs (e.g. Profile Info) only commit changes to API after the Apply button has been clicked. This is achieved with a whole plethora of helper actions and events and even a dedicated store called ProfileModificationStore.

The gist is => form changes => Update stores => Trigger rerender via trigger()
```dart
  void _onUpdateProfileName(String name) {
    _updateProfileName(name);
    _calculateCanCreateOrUpdate();
    trigger();
  }
```

=>Save button clicked => Make API call

```dart
Future<void> _onCreateProfile([_]) async {
    if (!canCreateOrUpdate) return;

    _modificationInProgress = true;
    trigger();

    // intentional delay for UX reasons
    final minimumCreateTime = Future.delayed(_minimumModifyDuration);

    final payload = CreateReportPayload(
    {omitted for clarity}
    );

    final createAction = _actions.createReport(payload);
    _sendCreateAnalytic();
    await Future.wait([createAction, minimumCreateTime]);

    _modificationInProgress = false;

    if (!_reportStore.errorCreatingReport) {
      _onClearModifyProfileData(); // Triggers
    } else {
      trigger();
    }
  }

  Future _onCreateReport(CreateReportPayload payload) async {
    if (_inProgressReportsByHash.containsKey(payload.label) ||
        _reportsByLabel.containsKey(payload.label)) {
      // Then we have a duplicate
      logger
          .fine('_onCreateReport - Aborting create because of duplicate label');
      return;
    }

    _errorCreatingReport = false;

    logger.fine('_onCreateReport - creating report');
    var newReport = await createReport(
    {omitted for clarity}
  );

    if (newReport == null) {
      logger.severe(ContextualMessage('Failed to create report', context: {
        'documentId': _orderedDocumentId,
        'label': payload.label,
        'taxonomyName': payload.taxonomy.name,
      }));
      _errorCreatingReport = true;
      trigger();
      return;
    } else {
      logger.fine(ContextualMessage('_onCreateReport - report created',
          context: {'id': newReport.id}));
    }

    _inProgressReportsByHash[payload.label] = newReport;

    logger.info("Created report '${payload.label}' for "
        "'${payload.taxonomy.name}'");

    _cacheReports([newReport]);
    _inProgressReportsByHash.remove(payload.label);
    _lastCreatedProfileId = newReport.id;

    _fetchDocumentEntityDataForReports([newReport]);
  }

```
This helper setup might cause additional baggages if that's not what's required
