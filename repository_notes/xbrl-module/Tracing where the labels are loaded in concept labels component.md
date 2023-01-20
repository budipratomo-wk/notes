# Tracing labels rendering method in the concept_labels_component
* Trigger directly on 
*  _fetchElrData 
* languageChanged action on ELR store

## Starting point: Render
```dart
  @override
  ReactElement render() {
    final hasLinkNode = _linkNode != null;

    if (hasLinkNode) {
      _getStoreData();
    }

    return (DialogBody()
      ..modifyProps(addUnconsumedProps)
      ..isFlexParent = true
      ..style = <String, dynamic>{
        'height': '47rem'
      }
    )(hasLinkNode ? _renderDefaultViewBody() : ConceptLabelEmptyBody()());
  }
}
```
It seems that the node that gets selected is stored in `outlineStore.selectedNodeId` that gets updated whenever 
action.SelectNodeId (or something similar) is called.


## Initialization: _getStoreData:
In this part, the concept and other related link nodes are retrieved. With this information, the section map and role map is built. All of these
are components for the rendering. The section map in particular is interesting, 
because each group of label controls correspond to one section. See `_renderPresentationGroups`
In summary, the flow goes like this: 
selectedLinkNode 
|> getConcept of selectedLinkNode
|> find all location of the same concept => build section map 
|> get label value for the role of node => build role map


Something that's not quite clear here: the fetching of the label values 
automatically assumes that it should be fetching the default language. 
What's the relationship between the node and the label values?
In a sense, is one node automatically linked to all the languages?
The ERD says that it's a one-to-many relationship. This probably implies that 
we just need to provide the enabledLanguages values there and it will fetch it


```dart
void _getStoreData() {
    final conceptBase =
        _conceptStore.getConcept(_taxonomy, _linkNode.conceptId);
    if (conceptBase is Concept) {
      _concept = conceptBase;
    }
    final linkNodes =
        _xbrlOutlineStore.getLinkNodesWithConcept(_linkNode.conceptId);
    _buildSectionMap(linkNodes);
    _buildRoleMap(linkNodes);
  }

  void _buildSectionMap(List<LinkTreeNode> linkNodes) {
    _sectionMap.clear();
    for (final linkNode in linkNodes) {
      final presSection = linkNode.getPresentationSectionNode();
      final presGroup = presSection.parent as PresentationGroupNode;
      final presGroupName = presGroup.outlineNodeMeta.name;
      final presSectionName = presSection.name;

      _sectionMap[presGroupName] ??= {};
      _sectionMap[presGroupName][presSectionName] ??= [];
      _sectionMap[presGroupName][presSectionName].add(linkNode);
    }
  }

  void _buildRoleMap(List<LinkTreeNode> linkNodes) {
    _roleLabelMap.clear();
    for (final node in linkNodes) {
      final result =
          _getLabelValueForRole(node.linkNodeDto.preferredLabelRole, node);
      _roleLabelMap[node.id] = _LabelRoleInfo()
        ..role = node.linkNodeDto.preferredLabelRole
        ..isCustomAllowed =
            _isCustomLabelAllowed(node.linkNodeDto.preferredLabelRole)
        ..isCustom = result.isCustom
        ..label = result.label;
    }
  }

Iterable<ReactElement> _renderPresentationGroups() {
    final groupLabelsDisplays = <ReactElement>[];

    _createLabelDisplaysForSections(
      String presGroupLabel,
      Map<String, List<LinkTreeNode>> nodesBySectionLabel,
    ) =>
        groupLabelsDisplays.add(
          _renderLabelControlsForPresGroup(
            presGroupLabel,
            nodesBySectionLabel,
          ),
        );

    _sectionMap.forEach(_createLabelDisplaysForSections);
    return groupLabelsDisplays;
  }
```

### Render the controls: _renderNodeControls
```dart
  ReactElement _renderNodeControls(String sectionName, LinkTreeNode linkNode) {
    final nodeControls = <ReactElement>[];
    nodeControls.add(_getRoleDropdown(linkNode));
    nodeControls.add(_getLabelControlForNode(linkNode));
    nodeControls.add(_getLabelLocationButtonGroup(linkNode));

    final formGroupLabelClasses = (ClassNameBuilder()
          ..add(WsCssMargin.BOTTOM_LARGE.className)
          ..add(WsCssMargin.TOP_BLOCK_GUTTER.className))
        .toClassName();
    final helperText = _renderTranslationHelperText(linkNode);
    return (FormGroup()
      ..addTestId('xbrl-preferred-label--node-control-group--${linkNode.id}')
      ..key = 'xbrl-preferred-label--node-control-group--${linkNode.id}'
      ..useFlexbox = true
      ..isDisabled = _nodeIsReadOnly(linkNode)
      ..isInlineV2 = true
      ..groupLabel = sectionName
      ..groupLabelClassName = formGroupLabelClasses
      ..hideGroupLabel = true
      ..hideLabel = false
    )(
      Block()(
        _renderControlHeaders(),
      ),
      (Block()
        ..key = linkNode.id
        ..crossAlign = BlockCrossAlign.START
        ..gutter = helperText == null
            ? BlockGutter.BOTTOM
            : BlockGutter.NONE
      )(nodeControls),
      helperText,
    );
  }
    ReactElement _getLabelControlForNode(LinkTreeNode linkNode) {
    ReactElement label;

    if (linkNode.hasLabelReference) {
      label = _renderLabelReference(linkNode);
    } else if (!_roleLabelMap[linkNode.id].isCustomAllowed &&
        !_roleLabelMap[linkNode.id].isCustom) {
      label = _renderStaticLabel(linkNode);
    } else {
      label = _renderTextInput(linkNode);
    }

    return (BlockContent()
      ..key = 'xbrl-concept-label-label-block'
      ..collapse = BlockCollapse.VERTICAL
      ..scroll = false
    )(
      label,
    );
  }
```
