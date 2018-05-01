All form states are internally updated by **ngrx-forms** through dispatching actions from the directives. While this is of course also possible for you there exist a set of update functions that can be used to update form states. This is mainly useful to change the state as a result of a different action in your reducer. Note that **ngrx-forms** is coded in such a way that no state references will change if nothing inside the state changes. It is therefore perfectly safe to repeatedly call any of the functions below and the state will be updated exactly once or not at all if nothing changed. Each function can be imported from `'ngrx-forms'` (e.g. `import { setValue } from 'ngrx-forms';`).

Below you will find a complete list of all update functions provided by **ngrx-forms**. Each section also shows how to use actions directly instead of the update functions (the examples directly call the `formStateReducer` but you can of course dispatch these actions from anywhere in your code).

#### Setting the value

The `setValue` update function takes a value and returns a projection function that sets the value of a form state. Setting the value of a group or array will also update the values of all its child states including adding and removing child states on the fly for added/removed properties/items. `setValue` has an overload that takes a state directly as the first parameter.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const updatedControl = setValue('new Value')(control);
const updatedControlUncurried = setValue(control, 'newValue');
const updatedControlViaAction = formStateReducer(control, new SetValueAction(control.id, 'newValue'));

// group
const group = createFormGroupState('group ID', { inner: '' });
const updatedGroup = setValue({ inner: 'newValue' })(group);
const updatedGroupUncurried = setValue(group, { inner: 'newValue' });
const updatedGroupViaAction = formStateReducer(group, new SetValueAction(group.id, { inner: 'newValue' }));

// array
const array = createFormArrayState('array ID', ['']);
const updatedArray = setValue(['newValue'])(array);
const updatedArrayUncurried = setValue(array, ['newValue']);
const updatedArrayViaAction = formStateReducer(array, new SetValueAction(array.id, ['newValue']));
```

#### Validating the value

The `validate` update function takes one or more validation functions and returns a projection function that sets the errors of a form state to the result of applying the given validation function(s) to the state's value. `validate` has an overload that takes a state directly as the first parameter. See [here](validation.md) for more details about validation.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const updatedControl = validate<string>(value => !value ? { missing: true } : {})(control);
const updatedControlUncurried = validate(control, value => !value ? { missing: true } : {});

// group
const group = createFormGroupState<{ inner: string }>('group ID', { inner: '' });
const updatedGroup = validate<{ inner: string }>(value => !value.inner ? { innerMissing: true } : {})(group);
const updatedGroupUncurried = validate(group, value => !value.inner ? { innerMissing: true } : {});

// array
const array = createFormArrayState<string>('array ID', ['']);
const updatedArray = validate<string[]>(value => value.length === 0 ? { missing: true } : {})(array);
const updatedArrayUncurried = validate(array, value => value.length === 0 ? { missing: true } : {});

// there is no corresponding action for `validate`, it uses `SetErrorsAction` internally
```

#### Setting errors

The `setErrors` update function takes one or more error objects and returns a projection function that sets the errors of a form state. `setErrors` has an overload that takes a state directly as the first parameter.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const updatedControl = setErrors({ missing: true })(control);
const updatedControlUncurried = setErrors(control, { missing: true });
const updatedControlViaAction = formStateReducer(control, new SetErrorsAction(control.id, { missing: true }));

// group
const group = createFormGroupState('group ID', { inner: '' });
const updatedGroup = setErrors({ innerMissing: true })(group);
const updatedGroupUncurried = setErrors(group, { innerMissing: true });
const updatedGroupViaAction = formStateReducer(group, new SetErrorsAction(group.id, { innerMissing: true }));

// array
const array = createFormArrayState('array ID', ['']);
const updatedArray = setErrors({ missing: true })(array);
const updatedArrayUncurried = setErrors(array, { missing: true });
const updatedArrayViaAction = formStateReducer(array, new SetErrorsAction(array.id, { missing: true }));
```

#### Enabling and disabling

The `enable` and `disable` update functions take a form state and enable/disable it. For groups and arrays also enables/disables all children. Disabling a control state will usually also disable the connected HTML form element (as long as the corresponding `FormViewAdapter` or `ControlValueAccessor` supports this). Disabled form states are also excluded from validation. Disabling a state will therefore clear all of its errors (i.e. making it always valid) and will remove all pending validations (thereby effectively cancelling those validations).

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const disabledControl = disable(control);
const enabledControl = enable(disabledControl);
const disabledControlViaAction = formStateReducer(control, new DisableAction(control.id));
const enabledControlViaAction = formStateReducer(disabledControlViaAction, new EnableAction(control.id));

// group
const group = createFormGroupState('group ID', { inner: '' });
const disabledGroup = disable(group);
const enabledGroup = enable(disabledGroup);
const disabledGroupViaAction = formStateReducer(group, new DisableAction(group.id));
const enabledGroupViaAction = formStateReducer(disabledGroupViaAction, new EnableAction(group.id));

// array
const array = createFormArrayState('array ID', ['']);
const disabledArray = disable(array);
const enabledArray = enable(disabledArray);
const disabledArrayViaAction = formStateReducer(array, new DisableAction(array.id));
const enabledArrayViaAction = formStateReducer(disabledArrayViaAction, new EnableAction(array.id));
```

#### Marking as dirty or pristine

The `markAsDirty` and `markAsPristine` update functions take a form state and mark it as dirty/pristine. For groups and arrays this also marks all children as dirty/pristine.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const dirtyControl = markAsDirty(control);
const pristineControl = markAsPristine(dirtyControl);
const dirtyControlViaAction = formStateReducer(control, new MarkAsDirtyAction(control.id));
const pristineControlViaAction = formStateReducer(dirtyControlViaAction, new MarkAsPristineAction(control.id));

// group
const group = createFormGroupState('group ID', { inner: '' });
const dirtyGroup = markAsDirty(group);
const pristineGroup = markAsPristine(dirtyGroup);
const dirtyGroupViaAction = formStateReducer(group, new MarkAsDirtyAction(group.id));
const pristineGroupViaAction = formStateReducer(dirtyGroupViaAction, new MarkAsPristineAction(group.id));

// array
const array = createFormArrayState('array ID', ['']);
const dirtyArray = markAsDirty(array);
const pristineArray = markAsPristine(dirtyArray);
const dirtyArrayViaAction = formStateReducer(array, new MarkAsDirtyAction(array.id));
const pristineArrayViaAction = formStateReducer(dirtyArrayViaAction, new MarkAsPristineAction(array.id));
```

#### Marking as touched or untouched

The `markAsTouched` and `markAsUntouched` update functions take a form state and mark it as touched/untouched. For groups and arrays this also marks all children as touched/untouched.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const touchedControl = markAsTouched(control);
const untouchedControl = markAsUntouched(touchedControl);
const touchedControlViaAction = formStateReducer(control, new MarkAsTouchedAction(control.id));
const untouchedControlViaAction = formStateReducer(touchedControlViaAction, new MarkAsUntouchedAction(control.id));

// group
const group = createFormGroupState('group ID', { inner: '' });
const touchedGroup = markAsTouched(group);
const untouchedGroup = markAsUntouched(touchedGroup);
const touchedGroupViaAction = formStateReducer(group, new MarkAsTouchedAction(group.id));
const untouchedGroupViaAction = formStateReducer(touchedGroupViaAction, new MarkAsUntouchedAction(group.id));

// array
const array = createFormArrayState('array ID', ['']);
const touchedArray = markAsTouched(array);
const untouchedArray = markAsUntouched(touchedArray);
const touchedArrayViaAction = formStateReducer(array, new MarkAsTouchedAction(array.id));
const untouchedArrayViaAction = formStateReducer(touchedArrayViaAction, new MarkAsUntouchedAction(array.id));
```

#### Marking as submitted or unsubmitted

The `markAsSubmitted` and `markAsUnsubmitted` update functions take a form state and mark it as submitted/unsubmitted. For groups and arrays this also marks all children as submitted/unsubmitted.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const submittedControl = markAsSubmitted(control);
const unsubmittedControl = markAsUnsubmitted(submittedControl);
const submittedControlViaAction = formStateReducer(control, new MarkAsSubmittedAction(control.id));
const unsubmittedControlViaAction = formStateReducer(submittedControlViaAction, new MarkAsUnsubmittedAction(control.id));

// group
const group = createFormGroupState('group ID', { inner: '' });
const submittedGroup = markAsSubmitted(group);
const unsubmittedGroup = markAsUnsubmitted(submittedGroup);
const submittedGroupViaAction = formStateReducer(group, new MarkAsSubmittedAction(group.id));
const unsubmittedGroupViaAction = formStateReducer(submittedGroupViaAction, new MarkAsUnsubmittedAction(group.id));

// array
const array = createFormArrayState('array ID', ['']);
const submittedArray = markAsSubmitted(array);
const unsubmittedArray = markAsUnsubmitted(submittedArray);
const submittedArrayViaAction = formStateReducer(array, new MarkAsSubmittedAction(array.id));
const unsubmittedArrayViaAction = formStateReducer(submittedArrayViaAction, new MarkAsUnsubmittedAction(array.id));
```

#### Resetting

The `reset` update function takes a form state and marks it as pristine, untouched, and unsubmitted. For groups and arrays this also marks all children as pristine, untouched, and unsubmitted.

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const updatedControl = markAsSubmitted(markAsTouched(markAsDirty(control)));
const resetControl = reset(updatedControl);
const resetControlViaAction = formStateReducer(updatedControl, new ResetAction(control.id));

// group
const group = createFormGroupState('group ID', { inner: '' });
const updatedGroup = markAsSubmitted(markAsTouched(markAsDirty(group)));
const resetGroup = reset(updatedGroup);
const resetGroupViaAction = formStateReducer(updatedGroup, new ResetAction(group.id));

// array
const array = createFormArrayState('array ID', ['']);
const updatedArray = markAsSubmitted(markAsTouched(markAsDirty(array)));
const resetArray = reset(updatedArray);
const resetArrayViaAction = formStateReducer(updatedArray, new ResetAction(array.id));
```

#### Focusing and Unfocusing

The `focus` and `unfocus` update functions take a form control state and mark it as focused/unfocused. If focus tracking is enabled on the connected HTML form element (via ```[ngrxEnableFocusTracking]="true"```) the element will also focused (via `.focus()`) or unfocused (via `.blur()`).

```typescript
const control = createFormControlState<string>('control ID', '');
const focusedControl = focus(control);
const unfocusedControl = unfocus(focusedControl);
const focusedControlViaAction = formStateReducer(control, new FocusAction(control.id));
const unfocusedControlViaAction = formStateReducer(focusedControlViaAction, new UnfocusAction(control.id));
```

#### Adding and removing group controls

The `addGroupControl` and `removeGroupControl` update functions take a form group state and add/remove a child state to/from it. `addGroupControl` takes a name and a value and returns a projection function that adds a child state with the given name and value to the form group state. `addGroupControl` has an overload that takes a form group state directly as the first parameter. `removeGroupControl` takes a name and returns a projection function that removes the child state with the given name from the form group state. `removeGroupControl` has an overload that takes a form group state directly as the first parameter.

```typescript
interface FormValueWithOptionalControl {
  someString?: string;
}

const group = createFormGroupState<FormValueWithOptionalControl>('group ID', {});
const groupWithControl = addGroupControl<FormValueWithOptionalControl>('someString', '')(group);
const groupWithoutControl = removeGroupControl<FormValueWithOptionalControl>('someString')(groupWithControl);
const groupWithControlUncurried = addGroupControl(group, 'someString', '');
const groupWithoutControlUncurried = removeGroupControl(groupWithControlUncurried, 'someString');
const groupWithControlViaAction = formStateReducer(group, new AddGroupControlAction(group.id, 'someString', ''));
const groupWithoutControlViaAction = formStateReducer(groupWithControlViaAction, new RemoveGroupControlAction(group.id, 'someString'));
```

#### Adding and removing array controls

The `addArrayControl` and `removeArrayControl` update functions take a form array state and add/remove a child state to/from it. `addArrayControl` takes a value and optionall an index and returns a projection function that adds a child control at the given index or at the end of a form array state. `addArrayControl` has an overload that takes a form array state directly as the first parameter. `removeArrayControl` takes an index and returns a projection function that removes the child state with the given index from the form array state. `removeArrayControl` has an overload that takes a form array state directly as the first parameter.

```typescript
const array = createFormArrayState('array ID', ['0', '2']);
const arrayWithControl = addArrayControl('1', 1)(array);
const arrayWithoutControl = removeArrayControl(1)(arrayWithControl);
const arrayWithControlUncurried = addArrayControl(array, '1', 1);
const arrayWithoutControlUncurried = removeArrayControl(arrayWithControlUncurried, 1);
const arrayWithControlViaAction = formStateReducer(array, new AddArrayControlAction(array.id, '1', 1));
const arrayWithoutControlViaAction = formStateReducer(arrayWithControlViaAction, new RemoveArrayControlAction(array.id, 1));
```

#### Setting user-defined properties

The `setUserDefinedProperty` update function takes a name and a value and returns a projection function that sets a user-defined property on a form state. `setUserDefinedProperty` has an overload that takes a state directly as the first parameter. User-defined properties can be used to attach any kind of metadata to a form state. Most often this is used to attach a list of valid values for validation (e.g. for an autocomplete control with a pre-defined list of allowed values).

```typescript
// control
const control = createFormControlState<string>('control ID', '');
const updatedControl = setUserDefinedProperty('allowedValues', ['foo', 'bar'])(control);
const updatedControlUncurried = setUserDefinedProperty(control, 'allowedValues', ['foo', 'bar']);
const updatedControlViaAction = formStateReducer(control, new SetUserDefinedPropertyAction(control.id, 'allowedValues', ['foo', 'bar']));

// group
const group = createFormGroupState('group ID', { inner: '' });
const updatedGroup = setUserDefinedProperty('allowedValues', ['foo', 'bar'])(group);
const updatedGroupUncurried = setUserDefinedProperty(group, 'allowedValues', ['foo', 'bar']);
const updatedGroupViaAction = formStateReducer(group, new SetUserDefinedPropertyAction(group.id, 'allowedValues', ['foo', 'bar']));

// array
const array = createFormArrayState('array ID', ['']);
const updatedArray = setUserDefinedProperty('allowedValues', ['foo', 'bar'])(array);
const updatedArrayUncurried = setUserDefinedProperty(array, 'allowedValues', ['foo', 'bar']);
const updatedArrayViaAction = formStateReducer(array, new SetUserDefinedPropertyAction(array.id, 'allowedValues', ['foo', 'bar']));
```

#### Updating groups
The `updateGroup` update function takes a partial object in the shape of the group's value where each key contains an update function for that child and returns a projection function that takes a group state, applies all the provided update functions recursively and recomputes the state of the group afterwards. `updateGroup` has an overload that takes a form group state directly as the first parameter. As with all the functions above this function does not change the reference of the group if none of the child update functions change any children. The best example of how this can be used is simple validation (see [validation](validation.md) for an explanation of the validation functions used in this example):

```typescript
export interface MyFormValue {
  someTextInput: string;
  someCheckbox: boolean;
  nested: { someNumber: number };
  someNumbers: number[];
}

const updateMyFormGroup = updateGroup<MyFormValue>({
  someTextInput: validate(required),
  nested: updateGroup<MyFormValue['nested']>({
    someNumber: validate(required, greaterThanOrEqualTo(2)),
  }),
  someNumbers: validate(minLength(3)),
});
```

The `updateMyFormGroup` function has a signature of `FormGroupState<MyFormValue> -> FormGroupState<MyFormValue>`. It takes a state, runs all validations, updates the errors, and returns the resulting state.

In addition, the `updateGroup` function allows specifying as many update function objects as you want and applies all of them after another. This is useful if you have dependencies between your update functions where one function's result may affect the result of another function. The following (contrived) example shows how to set the value of `someNumber` based on the `errors` of `someTextInput`.

```typescript
const updateMyFormGroup = updateGroup<MyFormValue>(
  {
    someTextInput: validate(required),
    nested: updateGroup<MyFormValue['nested']>({
      someNumber: validate(required, greaterThanOrEqualTo(2)),
    }),
    someNumbers: validate(minLength(3)),
  },
  {
    // note that the parent form state is provided as the second argument to update functions;
    // type annotations for parameters added for clarity but are inferred correctly otherwise
    nested: (nested: FormGroupState<MyFormValue['nested']>, myForm: FormGroupState<MyFormValue>) =>
      updateGroup<MyFormValue['nested']>(nested, {
        someNumber: (someNumber: FormControlState<number>) => {
          if (myForm.controls.someTextInput.errors.required) {
            // sets the control's value to 1 and clears all errors
            return setErrors(setValue(someNumber, 1), {});
          }

          return someNumber;
        },
      }),
  },
);
```

If you need to update the form state based on data not contained in the form state itself you can simply parameterize the form update function. In the following example we validate that `someNumber` is greater than some other number from the state.

```typescript
const createMyFormValidationFunction = (otherNumber: number) => updateGroup<MyFormValue>({
  nested: updateGroup<MyFormValue['nested']>({
    someNumber: validate(v => v > otherNumber ? {} : { tooSmall: otherNumber }),
  }),
});

export function appReducer(state = initialState, action: Action): AppState {
  let myForm = formGroupReducer(state.myForm, action);
  myForm = createMyFormValidationFunction(state.someOtherNumber)(myForm);
  if (myForm !== state.myForm) {
    state = { ...state, myForm };
  }

  switch (action.type) {
    case 'some action type':
      // modify state
      return state;

    case 'some action type of an action that changes `someOtherNumber`':
      // we need to update the form state as well since the parameters changed
      myForm = createMyFormValidationFunction(action.someOtherNumber)(state.myForm);
      return {
        ...state,
        someOtherNumber: action.someOtherNumber,
        myForm,
      };

    default: {
      return state;
    }
  }
}
```

#### Updating arrays

The `updateArray` update function takes an update function and returns a projection function that takes an array state, applies the provided update function to each element and recomputes the state of the array afterwards. `updateArray` has an overload that takes a form array state directly as the first parameter. As with all the functions above this function does not change the reference of the array if the update function does not change any child states. See the section below for an example of how this function can be used.

```typescript
const array = createFormArrayState('array ID', ['0', '2']);
const updatedArray = updateArray<string>(setValue('1'))(array);
const updatedArrayUncurried = updateArray(array, setValue('1'));
```

The `updateArrayWithFilter` update function works the same as `updateArray` except that it also takes a filter function that is applied to each array element to determine whether the update function should be applied. The filter function takes two arguments, the child state and the index of the child state. `updateArrayWithFilter` has an overload that takes a form array state directly as the first parameter.

```typescript
const array = createFormArrayState('array ID', ['0', '2']);
const updatedArray = updateArrayWithFilter<string>((s, idx) => s.value === '0' && idx === 0, setValue('1'))(array);
const updatedArrayUncurried = updateArrayWithFilter(array, (s, idx) => s.value === '0' && idx === 0, setValue('1'));
```

#### Combining reducer with updates

The `createFormStateReducerWithUpdate` function combines the [formStateReducer](type-inference.md) with one or more update functions and returns a reducer function that applies the provided update functions in order after reducing the form state with the action. However, the update functions are only applied if the form state changed as result of applying the action (this provides a performance improvement for large form states). If you need the update functions to be applied regardless of whether the state changed (e.g. because the update function closes over variables that may change independently of the form state) you can simply apply the update manually (e.g. `updateFunction(formStateReducer(state, action))`).

Combining all we have seen so far we could have a reducer that looks something like this:

```typescript
export interface MyFormValue {
  someTextInput: string;
  someCheckbox: boolean;
  nested: { someNumber: number };
  someNumbers: number[];
}

export interface AppState {
  myForm: FormGroupState<MyFormValue>;
}

const FORM_ID = 'some globally unique string';

const initialFormState = createFormGroupState<MyFormValue>(FORM_ID, {
  someTextInput: '',
  someCheckbox: false,
  nested: {
    someNumber: 0,
  },
  someNumbers: [],
});

const initialState: AppState = {
  myForm: initialFormState,
};

const validateAndUpdateFormState = updateGroup<MyFormValue>({
  someTextInput: validate(required),
  nested: updateGroup<MyFormValue['nested']>({
    someNumber: validate(required, greaterThanOrEqualTo(2)),
  }),
  someNumbers: validate(minLength(3)),
}, {
    nested: (nested, myForm) =>
      updateGroup<MyFormValue['nested']>(nested, {
        someNumber: someNumber => {
          if (myForm.controls.someTextInput.errors.required) {
            return setErrors(setValue(someNumber, 1), {});
          }

          return someNumber;
        },
      }),
  });

const myFormReducer = createFormStateReducerWithUpdate<MyFormValue>(validateAndUpdateFormState);

export function appReducer(state = initialState, action: Action): AppState {
  const myForm = myFormReducer(state.myForm, action);
  if (myForm !== state.myForm) {
    state = { ...state, myForm };
  }

  switch (action.type) {
    case 'some action type':
      // modify state
      return state;

    default: {
      return state;
    }
  }
}
```

#### Updating states recursively

Sometimes it is useful to apply an update function to all controls in a group or array recursively. The `updateRecursive` update function takes an update function and returns a projection function that takes any state and applies the provided update function to all its children, its children's children etc. and finally to the state itself. This means when the update function is called for a certain state all of its children will have already been updated. The provided update function takes 2 parameters, the state to update and its parent state. For the top-level state the state itself is passed as the second parameter.

Below you can find an example of how this function can be used. In this example we want to block all form inputs temporarily (e.g. while submitting the form). This can be done by disabling the form state at the root. However, when we unblock all inputs we want their enabled/disabled state to be reset to what it was before blocking the inputs. This could be done by simply storing a complete copy of the state (which might take a lot of space depending on the size of the form state). However, the example below uses a different method. We use the `setUserDefinedProperty` update function to store the enabled/disabled state before blocking the inputs and later restore them to the state they were in.

```typescript
export function appReducer(state = initialState, action: Action): AppState {
  switch (action.type) {
    case 'BLOCK_INPUTS': {
      let myForm = updateRecursive(state.myForm, s => setUserDefinedProperty('wasDisabled', s.isDisabled)(s));
      myForm = disable(myForm);

      return {
        ...state,
        myForm,
      };
    }

    case 'UNBLOCK_INPUTS': {
      let myForm = enable(state.myForm);
      myForm = updateRecursive(myForm, s => s.userDefinedProperties.wasDisabled ? disable(s) : s);

      return {
        ...state,
        myForm,
      };
    }

    default: {
      return state;
    }
  }
}
```