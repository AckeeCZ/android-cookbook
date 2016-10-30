# Cooking Rules

## General rules
We use:
- Android Stuido as a default development tool
- gradle as a default building tool
- Proguard for obfuscation
- the newest SDK if possible
- Lint tool to remove errors and warnings

## Programming style
We use:
- blocking operations (loading, db-transactions) asynchronously
- snackbar instead of toasts
- reactive chaining methods instead of timers and handlers


# Conventions

## General guidelines
- overridden methods are always marked with the `@Override` annotation
- the scope of a variable should be kept to a minimum. If a variable is shared among more than one component, it should be put into a separate class (`Constants.java`)
- use the `Log` method with the name of the class as a tag and define it as a `static final` field at the top of the file
- code line should not exceed 100 characters
- number of lines of a single method should not exceed 35
- `mPrefix` is not used anymore
- we use 4 space indents for blocks
- braces go on the same line as the code before them
- braces around all statements are required ==even if the condition and its body fit on one line!==

## Code documentation
We comment:
- class headers, including the signature of the author and the date of creation
- public methods
- complex return types
- all workarounds and substandard practices that are critical to understand when editing
- exceptions and possible errors that may occur
- nullable types (via `@Nullable` annotation)


We usually don't comment:
- overridden methods (`onCreate()`, `onPause()`)
- getters and setters
- self-explanatory methods (`calcWidth()`)
- xml layouts



## Naming conventions
- for strings we use this format: `screen_viewtype_function` (e.g. `contact_detail_title`)
- for constants we use uppercase letters with underscores (e.g. `static final int DEFAULT_INTERVAL = 12;`)



### Naming conventions for identifiers:

| Type   | Prefix            |		Example               |
|--------------| ------------------|------------------|
|Button   | `btn_`             | `btn_start`    |
|ImageView   | `img_`             | `img_detail`    |
|TextView   | `txt_`             | `txt_password`    |

### Naming conventions for strings:

| Type   | Suffix/Prefix            |		Example               |
|--------------| ------------------|------------------|
|Title   | _title_             | mainpage_title    |
|Error   | _error_             | error_general    |
|Message   | _msg_             | mainpage_info_msg    |

### Naming conventions for components:

| Type   | Prefix  |  XML layout      | Java class |
|--------------| ------------------|------------------|---|
|Activity   | `activity_` | `activity_main.xml`    | MainActivity.class |
|Fragment   | `fragment_` | `fragment_detail.xml`    | DetailFragment.class |
|Dialog   | `dialog_` | `dialog_info.xml`    | InfoDialog.class |
|View   | `view_` | `view_header.xml`    | HeaderView.class |


### Class member ordering
1. Constants
2. Fields
3. Interfaces
4. Constructors
5. Overridden methods (public or private)
6. Public methods
7. Private methods
8. Inner classes
