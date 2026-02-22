# GTM Template Test Reference & Examples

## Test Format

The `___TESTS___` section uses YAML format with JavaScript code blocks:

```yaml
scenarios:
- name: "Descriptive test name"
  code: |-
    // JavaScript test code here
    const mockData = {...};
    mock('apiName', function() {...});
    runCode(mockData);
    assertApi('apiName').wasCalled();
```

## Test APIs

### `runCode(data)`

Executes the template's sandboxed JS with mock data. The `data` parameter simulates template parameter values.

```javascript
const mockData = {
  dataLayerEventName: 'my_event',
  enableFeature: true,
  trackSomething: false
};
runCode(mockData);
```

**Key names in `mockData` must match the `name` field in `___TEMPLATE_PARAMETERS___`.**

### `mock(apiName, implementation)`

Replaces a sandboxed API with a mock. Mocks reset between test scenarios.

```javascript
// Mock with a function
mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
  onSuccess(); // Simulate successful load
});

// Mock with a simple return value
mock('copyFromWindow', 'existingValue');
```

### `mockObject(apiName, objectMock)`

Replaces an object-returning API with a mock object.

```javascript
mockObject('templateStorage', {
  getItem: function(key) { return null; },
  setItem: function(key, value) { },
  removeItem: function(key) { }
});
```

### `assertApi(apiName)`

Assert that a sandboxed API was called (or not).

```javascript
assertApi('gtmOnSuccess').wasCalled();
assertApi('gtmOnFailure').wasNotCalled();
assertApi('setInWindow').wasCalled();
assertApi('injectScript').wasCalled();
```

### `assertThat(value)`

Value-based assertions.

```javascript
assertThat(savedConfig.eventName).isEqualTo('my_event');
assertThat(savedConfig.enabled).isTrue();
assertThat(savedConfig.disabled).isFalse();
assertThat(savedConfig.missing).isUndefined();
assertThat(result).isString();
assertThat(items).hasLength(3);
```

### `fail(message)`

Explicitly fail a test.

```javascript
if (unexpectedCondition) {
  fail('This should not have happened');
}
```

---

## Common Test Patterns

### Pattern 1: Basic Success Test

Verify the template runs and calls `gtmOnSuccess`:

```yaml
- name: Tag executes successfully with default settings
  code: |-
    const mockData = {
      dataLayerEventName: 'my_event',
      enableFeature: true
    };

    mock('setInWindow', function(key, value, override) {
      return true;
    });

    mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
      onSuccess();
    });

    runCode(mockData);
    assertApi('setInWindow').wasCalled();
    assertApi('injectScript').wasCalled();
    assertApi('gtmOnSuccess').wasCalled();
```

### Pattern 2: Failure Test

Verify the template handles script load failure:

```yaml
- name: Tag calls gtmOnFailure when script fails to load
  code: |-
    const mockData = {
      dataLayerEventName: 'my_event'
    };

    mock('setInWindow', function(key, value, override) {
      return true;
    });

    mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
      onFailure();
    });

    runCode(mockData);
    assertApi('gtmOnFailure').wasCalled();
```

### Pattern 3: Config Object Verification

Capture and verify the config passed via `setInWindow`:

```yaml
- name: Config object reflects user parameter selections
  code: |-
    let savedConfig;

    const mockData = {
      dataLayerEventName: 'custom_event',
      enableFeatureA: true,
      enableFeatureB: false,
      enableFeatureC: true
    };

    mock('setInWindow', function(key, value, override) {
      if (key === '_myConfig') {
        savedConfig = value;
      }
      return true;
    });

    mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
      onSuccess();
    });

    runCode(mockData);

    assertThat(savedConfig.dataLayerEventName).isEqualTo('custom_event');
    assertThat(savedConfig.features.featureA).isTrue();
    assertThat(savedConfig.features.featureB).isUndefined();
    assertThat(savedConfig.features.featureC).isTrue();
```

### Pattern 4: Default Value Test

Verify defaults work when minimal data is provided:

```yaml
- name: Uses default event name when not specified
  code: |-
    let savedConfig;

    const mockData = {};

    mock('setInWindow', function(key, value, override) {
      if (key === '_myConfig') {
        savedConfig = value;
      }
      return true;
    });

    mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
      onSuccess();
    });

    runCode(mockData);
    assertThat(savedConfig.dataLayerEventName).isEqualTo('default_name');
```

### Pattern 5: Script URL Verification

Verify the correct script URL is used:

```yaml
- name: Injects script from correct CDN URL
  code: |-
    let injectedUrl;

    mock('setInWindow', function() { return true; });

    mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
      injectedUrl = url;
      onSuccess();
    });

    runCode({dataLayerEventName: 'test'});
    assertThat(injectedUrl).isEqualTo('https://cdn.jsdelivr.net/gh/MyOrg/cdn@1.0.0/my-script.js');
```

### Pattern 6: Pixel/sendPixel Test

Verify pixel URL construction:

```yaml
- name: Sends pixel with correct parameters
  code: |-
    let pixelUrl;

    mock('sendPixel', function(url, onSuccess, onFailure) {
      pixelUrl = url;
      onSuccess();
    });

    runCode({
      trackingId: 'ABC123',
      eventName: 'pageview'
    });

    assertThat(pixelUrl).isEqualTo('https://collect.example.com/pixel?id=ABC123&event=pageview');
    assertApi('gtmOnSuccess').wasCalled();
```

### Pattern 7: Cookie Read Test

Verify cookie reading behavior:

```yaml
- name: Reads client ID from cookie
  code: |-
    mock('getCookieValues', function(name) {
      if (name === '_ga') return ['GA1.2.123456.789012'];
      return [];
    });

    const result = runCode({cookieName: '_ga'});
    assertThat(result).isEqualTo('GA1.2.123456.789012');
```

### Pattern 8: Template for Checkbox-Based Event Selection

Complete test for a template that uses checkboxes to select which events to track:

```yaml
- name: Only selected events are included in config
  code: |-
    let savedConfig;

    const mockData = {
      dataLayerEventName: 'widget',
      trackEventA: true,
      trackEventB: false,
      trackEventC: true,
      trackEventD: true
    };

    mock('setInWindow', function(key, value, override) {
      if (key === '_widgetConfig') {
        savedConfig = value;
      }
      return true;
    });

    mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
      onSuccess();
    });

    runCode(mockData);

    // Enabled events should be present
    assertThat(savedConfig.allowedEvents.eventA).isTrue();
    assertThat(savedConfig.allowedEvents.eventC).isTrue();
    assertThat(savedConfig.allowedEvents.eventD).isTrue();

    // Disabled events should be absent (undefined, not false)
    assertThat(savedConfig.allowedEvents.eventB).isUndefined();

    assertApi('gtmOnSuccess').wasCalled();
```

---

## Tips

- **Test names** should describe what the test verifies, not how it works
- **One assertion focus per test** — Better to have more focused tests than one giant test
- **Mock data key names** must exactly match `name` fields in `___TEMPLATE_PARAMETERS___`
- **Always mock `injectScript`** if the template uses it — unmocked calls will hang
- **Call `onSuccess()` in mocks** to trigger the callback chain (unless testing failure)
- **Capture values** via mock functions using closure variables (`let savedConfig;`)
