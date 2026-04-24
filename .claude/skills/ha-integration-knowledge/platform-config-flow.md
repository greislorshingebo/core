# Config Flow Platform Knowledge

## Overview
Config flows handle the UI-based configuration of integrations in Home Assistant.
They replace legacy `configuration.yaml` setup for user-facing integrations.

## Key Classes
- `ConfigFlow`: Base class for integration setup flows
- `OptionsFlow`: Base class for integration options (post-setup configuration)
- `FlowResult`: TypedDict representing the result of a flow step

## Required Implementation
```python
class MyIntegrationConfigFlow(ConfigFlow, domain=DOMAIN):
    VERSION = 1

    async def async_step_user(
        self, user_input: dict[str, Any] | None = None
    ) -> FlowResult:
        errors: dict[str, str] = {}
        if user_input is not None:
            # validate and create entry
            return self.async_create_entry(title="My Integration", data=user_input)
        return self.async_show_form(
            step_id="user",
            data_schema=vol.Schema({vol.Required(CONF_HOST): str}),
            errors=errors,
        )
```

## Quality Scale Rules
- Config flow must handle `already_configured` abort for unique entries
- Use `self.async_set_unique_id()` and `self._abort_if_unique_id_configured()`
- Validate credentials/connectivity in the flow, not just at setup
- Provide meaningful error keys that map to `strings.json` translations
- Options flow should be registered via `async_get_options_flow` class method

## Common Error Keys
- `cannot_connect`: Host unreachable or connection refused
- `invalid_auth`: Bad credentials
- `unknown`: Catch-all for unexpected exceptions
- `already_configured`: Device/service already set up

## Testing Requirements
- Test happy path (successful creation)
- Test each error condition
- Test abort conditions (duplicate unique ID)
- Use `hass.config_entries.flow.async_init` and `async_configure` in tests
