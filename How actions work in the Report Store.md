#XBRL-FE
All actions are registered under a subscription that will trigger an event listener, just like all Flux-based store actions. However, what's interesting is what happens in the event listener.


See _updateReportDocumentSet for example


```dart
Future _updateReportDocumentSet(ReportDTO report, String documentId) async {
    var operations = buildAddToSetOperations(<ReportDTO>[report],
        FieldName.Documents, <String>[documentId], report.id);
    await _xbrlDataApi.executeChange(operations);
  }
```


this first builds the AddToSetOperation that will be executed by the `executeChange()` method.  

Here's the definition of the buildAddToSetOperations: 

```dart
List<Operation> buildAddToSetOperations(Iterable<dynamic> objectsToUpdate,
    int fieldName, Iterable<dynamic> additions, String reportId) {
  var operations = <Operation>[];
  for (var object in objectsToUpdate) {
    if (object == null) continue;
    var objectType = getObjectType(object);
    var op = AddToSet()
      ..id = object.id as String
      ..type = objectType
      ..fieldName = fieldName
      ..additions = List.from(additions.map<ChangeValue>(buildChangeValue));

    operations.add(Operation()..addToSetOp = op);
  }
  return operations;
}
```


Here's the definition of the execute change method: 

```dart
  /// Performs an `executeChange` with the given [changes].
  /// [spanContext], if provided, will be used to construct a new Frugal
  /// [frugal.FContext] to be sent with the backend call.
  /// [errorContext], if provided, will be merged into the [ContextualMessage]
  /// context that will be created and sent to the logger if an error occurs.
  /// The following set of keys are reserved and should not be used in
  /// [errorContext]: {'correlationId', 'changeResult', 'result'}.
  /// [internalUser], if provided, notifies the backend that
  /// the request user is a Workiva user and should have access to
  /// internal only entities. Note that this only applies if the change list
  /// contains internal-only-capable entity types (ReportReview or FactReview).
  /// If [internalUser] is not provided, the backend will handle this as if it was
  /// set to true.
  Future<change_request.ChangeResult> executeChange(
      List<change_request.Operation> changes,
      {SpanContext spanContext,
      Map<String, dynamic> errorContext,
      bool internalUser}) async {
    if (changes.isEmpty) return null;

    var context = await _contextGenerator.newContext(spanContext: spanContext);
    var changeSet = change_request.ChangeList()..operations = changes;
    if (internalUser != null) changeSet.internalUser = internalUser;
    change_request.ChangeResult changeResult;

    logger.finest("Sending changeSet to x2: $changeSet");
    try {
      changeResult = await _xbrlWriteService.executeChange(context, changeSet);
      if (changeResult.result == change_request.ChangeResultType.Success) {
        logger.finest("executeChange() returned $changeResult");
      } else {
        _logExecuteChangeError('executeChange() failed', context.correlationId,
            errorContext: errorContext, result: changeResult);
      }
    } catch (e, s) {
      _logExecuteChangeError(
          'executeChange() raised an exception', context.correlationId,
          errorContext: errorContext, exception: e, stackTrace: s);
      // Create a fake result for the caller to handle instead of letting the
      // exception propagate.
      changeResult = change_request.ChangeResult()
        ..result = change_request.ChangeResultType.UnknownError
        ..message = 'An exception occurred while processing the request.'
        ..newIdMap = {};
    }
    return changeResult;
  }
```


and after the execution change is successful, then update the UI with another action + store combo. 
