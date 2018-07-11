# Android Support library - preference v7 bugfix

> **BREAKING CHANGE** in 26.1.0.3: The custom attribute names for extra preference types use `pref_` prefix in order to avoid name collision with other libraries. See the changelog for more details.

This library is meant to fix some of the problems found in the official support preference-v7 library. Also, there are [new preference types](#extra-types) available, such as `RingtonePreference`, `DatePickerPreference`, and `TimePickerPreference`.

[ ![Download](https://api.bintray.com/packages/gericop/maven/com.takisoft.fix%3Apreference-v7/images/download.svg) ](https://bintray.com/gericop/maven/com.takisoft.fix%3Apreference-v7/_latestVersion)

### Donation

If you would like to support me, you may donate some small amount via PayPal.

[ ![Buy me a coffee](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/donate.png)](https://www.paypal.me/korossyg/0eur)

---

## How to use the library?
### 1. Add gradle dependency
First, **remove** the unnecessary lines of preference-v7 and preference-v14 from your gradle file as the bugfix contains both of them:
```gradle
implementation 'com.android.support:preference-v7:27.1.1'
implementation 'com.android.support:preference-v14:27.1.1'
```
And **add** this single line to your gradle file:
```gradle
implementation 'com.takisoft.fix:preference-v7:27.1.1.1'
```
> Notice the versioning: the first three numbers are *always* the same as the latest official library while the last number is for own updates. I try to keep it up-to-date but if, for whatever reasons, I wouldn't notice the new support library versions, just issue a ticket.

### 2. Use the appropriate class as your fragment's base
You can use either `PreferenceFragmentCompat` or `PreferenceFragmentCompatDividers`. The former is the fixed version of the original fragment while the latter is an extended one where you can set the dividers using the divider flags. The `PreferenceFragmentCompatDividers` is the recommended approach as it uses the updated Material Design guidelines to create the appropriate divider config.

#### Option 1 - `PreferenceFragmentCompat`
```java
import com.takisoft.fix.support.v7.preference.PreferenceFragmentCompat;

public class MyPreferenceFragment extends PreferenceFragmentCompat {

    @Override
    public void onCreatePreferencesFix(@Nullable Bundle savedInstanceState, String rootKey) {
        setPreferencesFromResource(R.xml.settings, rootKey);
	
	// additional setup
    }
}
```
> **Warning!** Watch out for the correct package name when importing `PreferenceFragmentCompat`, it should come from `com.takisoft.fix.support.v7.preference`.
---
#### Option 2 - `PreferenceFragmentCompatDividers`
```java
import com.takisoft.fix.support.v7.preference.PreferenceFragmentCompatDividers;

public class MyPreferenceFragment extends PreferenceFragmentCompatDividers {

    @Override
    public void onCreatePreferencesFix(@Nullable Bundle savedInstanceState, String rootKey) {
        setPreferencesFromResource(R.xml.settings, rootKey);

	// additional setup
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, @Nullable Bundle savedInstanceState) {
        try {
            return super.onCreateView(inflater, container, savedInstanceState);
        } finally {
            setDividerPreferences(DIVIDER_PADDING_CHILD | DIVIDER_CATEGORY_AFTER_LAST | DIVIDER_CATEGORY_BETWEEN);
        }
    }
}
```

### 3. Add preferenceTheme
```xml
<style name="Theme.MyTheme" parent="@style/Whatever">
    <!-- [...] -->
    <item name="preferenceTheme">@style/PreferenceThemeOverlay.v14.Material</item>
</style>
```

### 4. That's it!
Now you can enjoy using the support preferences API without losing all your hair.

---

## Extra types

There are additional preferences not part of the official support library, but decided to add them to some extra libraries. You can add all of them to your project using

```gradle
implementation 'com.takisoft.fix:preference-v7-extras:27.1.1.1'
```

or one or more groups:

Preference | Dependency | Preview
-|-|-
[`RingtonePreference`](https://github.com/Gericop/Android-Support-Preference-V7-Fix/wiki/Preference-types#ringtonepreference) | `compile 'com.takisoft.fix:preference-v7-ringtone:27.1.1.1'` | ![API 26](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/ringtone_api26.png)
[`DatePickerPreference`](https://github.com/Gericop/Android-Support-Preference-V7-Fix/wiki/Preference-types#datepickerpreference) | `compile 'com.takisoft.fix:preference-v7-datetimepicker:27.1.1.1'` | ![API 26](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/datepicker_api26.png)
[`TimePickerPreference`](https://github.com/Gericop/Android-Support-Preference-V7-Fix/wiki/Preference-types#timepickerpreference) | `compile 'com.takisoft.fix:preference-v7-datetimepicker:27.1.1.1'` | ![API 26](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/timepicker_api26.png)
[`ColorPickerPreference`](https://github.com/Gericop/Android-Support-Preference-V7-Fix/wiki/Preference-types#colorpickerpreference) | `compile 'com.takisoft.fix:preference-v7-colorpicker:27.1.1.1'` | ![API 26](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/colorpicker_api26_fixed.png)
[`SimpleMenuPreference`](https://github.com/Gericop/Android-Support-Preference-V7-Fix/wiki/Preference-types#simplemenupreference) | `compile 'com.takisoft.fix:preference-v7-simplemenu:27.1.1.1'` | ![API 26](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/simplemenu_api26.png)

---

## Custom solutions
### Dividers
If you use `PreferenceFragmentCompatDividers` as your base class for the preference fragment, you can use 3 new methods to customize the dividers:
- `setDivider(Drawable drawable)`: Sets a custom `Drawable` as the divider.
- `setDividerHeight(int height)`: Sets the height of the drawable; useful for XML resources.
- `setDividerPreferences(int flags)`: Sets where the dividers should appear. Check the documentation of the method for more details about the available flags.

### Hijacked `EditTextPreference`
The support implementation of `EditTextPreference` ignores many of the basic yet very important attributes as it doesn't forward them to the underlying `EditText` widget. In my opinion this is a result of some twisted thinking which would require someone to create custom dialog layouts for some simple tasks, like showing a numbers-only dialog. This is the main reason why the `EditTextPreference` gets hijacked by this lib: it replaces certain aspects of the original class in order to forward all the XML attributes set (such as `inputType`) on the `EditTextPreference` to the `EditText`, and also provides a `getEditText()` method so it can be manipulated directly.

The sample app shows an example of setting (via XML) and querying (programmatically) the input type of the `EditTextPreference`:
```xml
<EditTextPreference
    android:inputType="phone"
    android:key="edit_text_test" />
```

```java
EditTextPreference etPref = (EditTextPreference) findPreference("edit_text_test");
if (etPref != null) {
    int inputType = etPref.getEditText().getInputType();
    // do something with inputType
}
```
> **Note!** Watch out for the correct package name when importing `EditTextPreference`, it should come from `com.takisoft.fix.support.v7.preference`. If you import from the wrong package (i.e. `android.support.v7.preference`), the `getEditText()` method will not be available, however, the XML attributes will still be forwarded and processed by the `EditText`.


### PreferenceCategory's bottom margin reduce
Some people found the preference category's bottom margin too big, so now you can change it from the styles by setting the `preferenceCategory_marginBottom` value in your theme, for example:
```xml
<item name="preferenceCategory_marginBottom">0dp</item>
```

---

## Version
The current stable version is **27.1.1.1**.

## Notes #
This demo / bugfix is set to work on API level 14+.

---

## Sample

_Material design - everywhere._

API 15 | API 21 | API 26
-|-|-
![API 15](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/base_api15.png) | ![API 21](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/base_api21.png) | ![API 26](https://raw.githubusercontent.com/Gericop/Android-Support-Preference-V7-Fix/master/images/base_api26.png)

---

### Changelog

**2018-05-11**

New version: 27.1.1.1 (based on v27.1.1)

- No support preferences related changes in the support library.
- Bug fixes (#153, #149, #155, #152).

**2018-04-10**

New version: 27.1.1.0 (based on v27.1.1)

- No support preferences related changes in the support library.
- Some bug fixes (see #147 for more info).

> For older changelogs, check out the [CHANGELOG](CHANGELOG.md) file.

Feel free to ask / suggest anything on this page by creating a ticket (*issues*)!

# License notes #
You can do whatever you want except where noted.
