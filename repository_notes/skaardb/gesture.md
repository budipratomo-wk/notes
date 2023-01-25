---
title: repository_notes/skaardb/gesture
date:  2023-01-23
---

# Relevant files: 
- operation.go
- model.go


# Code flow example

```go
// Base is an interface for the most basic of operations.
// Eventually all "Handler"s will decompose themselves into a list of Ops
// which will be applied to the indice.
// Ops should be operations which directly modify indices and cannot be
// decomposed into smaller pieces.
type Base interface {
	Apply(docCtx *document.Context) error
	Trim(docCtx *document.Context) (isEmpty bool, trimmedBy []operror.TrimmedBy, err error)
	Validate(docCtx *document.Context) error
}

//Gets implemented by a new base operation
type SetMinApproveTrackChangesPerm struct {
	Value model.MinApproveTrackChangesPerm

	baseNoTrim
}

/// And defining a handler 
func (o *SetMinApproveTrackChangesPerm) Apply(docCtx *document.Context) error {
	return docCtx.Services.DocumentMetadataIndex.SetMinApproveTrackChangesPerm(o.Value)
}


// These handlers are then run as base operations upon decomposition of a gesture
// example: set_document_properties.go 
if g.MinApproveTrackChangesPerm != nil {
	oldMinApproveTrackChangesPerm, err := docCtx.Services.DocumentMetadataIndex.GetMinApproveTrackChangesPerm()
	if err != nil {
		return nil, err
	}
	if *g.MinApproveTrackChangesPerm != oldMinApproveTrackChangesPerm {
		ops = append(ops, &operation.SetMinApproveTrackChangesPerm{Value: *g.MinApproveTrackChangesPerm})
	}
}
```







