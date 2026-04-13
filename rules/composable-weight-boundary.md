---
title: Composable Weight and Boundary
impact: HIGH
impactDescription: prevents one composable from owning multiple independently changing business capabilities
type: capability
tags: composable, boundary, granularity, orchestration, coupling, architecture
---

# Composable Weight and Boundary

**Impact: HIGH** — prevents one composable from owning multiple independently changing business capabilities

An orchestration composable may coordinate a workflow, but it should not absorb every detailed concern in that workflow. A composable becomes too heavy when it combines multiple business capabilities that could change, be tested, or be reused independently.

## Symptoms

- One composable owns workflow state, registration or coordination, validation orchestration, submission workflow, side-effect messaging, and reset logic all together
- The composable returns a very wide API, but each consumer only uses a subset
- Editing one branch of the workflow risks breaking unrelated branches in the same composable
- A feature-specific composable keeps growing because every new requirement is added to the same container
- Small reusable units are missing, so related components duplicate parts of the same workflow logic

## Decision Rule

A composable is too heavy when it does **two or more** of the following for **independently changing concerns**:

- owns primary domain state for multiple business stages
- defines validation rules or validation orchestration
- performs async submission or persistence workflows
- manages side-effect-only UI feedback such as message or notification coordination
- implements reset, rollback, or hydration logic for the whole feature
- exposes unrelated actions that are consumed by different parts of the view tree

If those concerns evolve independently, split them into smaller cohesive composables and let the orchestration composable coordinate them.

## Problem Pattern

```ts
export function useApiForm() {
  // step state
  const currentStep = ref(0)
  const basicInfo = reactive({})
  const sqlConfig = reactive({})
  const requestParams = ref([])

  // validation
  function validateCurrentStep() {}
  function validateAll() {}

  // submission
  async function submit() {}

  // side effects
  function showSuccessMessage() {}

  // reset
  function resetAll() {}

  return {
    currentStep,
    basicInfo,
    sqlConfig,
    requestParams,
    validateCurrentStep,
    validateAll,
    submit,
    showSuccessMessage,
    resetAll,
  }
}
```

## Fix

```ts
export function useApiFormSteps() {
  const currentStep = ref(0)
  const basicInfo = reactive({})
  const sqlConfig = reactive({})
  const requestParams = ref([])

  return { currentStep, basicInfo, sqlConfig, requestParams }
}

export function useApiFormValidation() {
  function validateCurrentStep() {}
  function validateAll() {}

  return { validateCurrentStep, validateAll }
}

export function useApiFormSubmission() {
  async function submit() {}
  function resetAll() {}

  return { submit, resetAll }
}

export function useApiForm() {
  const steps = useApiFormSteps()
  const validation = useApiFormValidation()
  const submission = useApiFormSubmission()

  return { ...steps, ...validation, ...submission }
}
```

## Review Guidance

- Do not flag a composable just because it is page-level or moderately large.
- Do flag it when it mixes multiple independently changing business capabilities into one unit.
- Prefer recommendations like "split workflow state / validation / submission into smaller units" over vague advice like "refactor this hook."
- If the top-level composable still provides value after the split, classify it as a valid orchestration composable rather than a hollow wrapper.

## Reference

- [Vue Composables](https://vuejs.org/guide/reusability/composables.html)
