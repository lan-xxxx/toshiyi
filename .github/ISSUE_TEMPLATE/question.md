---
name: Source / product question
description: Ask a grounded question about octo-server source or product behavior
title: "[Question] "
labels: ["type/question", "status/new"]
body:
  - type: textarea
    id: question
    attributes:
      label: Question
    validations:
      required: true
  - type: textarea
    id: context
    attributes:
      label: Context
