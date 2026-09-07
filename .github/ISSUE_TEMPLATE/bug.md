---
name: Bug report
description: Report a reproducible defect in octo-server behavior
title: "[Bug] "
labels: ["type/bug", "status/new"]
body:
  - type: textarea
    id: summary
    attributes:
      label: Summary
    validations:
      required: true
  - type: textarea
    id: steps
    attributes:
      label: Reproduction Steps
      value: |
        1.
        2.
        3.
    validations:
      required: true
  - type: textarea
    id: expected
    attributes:
      label: Expected Behavior
    validations:
      required: true
  - type: textarea
    id: actual
    attributes:
      label: Actual Behavior
    validations:
      required: true
