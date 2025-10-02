# Home Assistant Automation Improvements Log

## Project Overview
This document tracks improvements made to the Home Assistant automations.yaml file to enhance maintainability, reliability, and functionality.

---

## Completed Improvements

### 1. Removed Duplicate Automations
- **Dishwasher monitoring**: Removed duplicate "Dishwasher Turns On" and "Dishwasher Turns Off" automations (kept the newer "Dishwasher Power Monitoring" automation that handles both)
- **Kitchen motion light**: Removed complex "Kitchen Motion Light with Auto-Off + Safe Manual Override" and companion automations, enhanced the simpler version instead

### 2. Fixed Incorrect Notification Messages
- **Refrigerator sensor notification** (line 193): Changed message from "Refrigerator door is open" to "Refrigerator contact sensor is offline" when sensor becomes unavailable
  - Issue: Notification triggered when sensor went offline, not when door was actually open

### 3. Replaced Cryptic Device/Entity IDs with Friendly Names
- `switch.f74e0cd0e553902fdc5f064b6fdd9aba` → `switch.laundry_area_mini_smart_plug`
- `device_id: c201f42aed47f91878a153f9248ac77d` → `lock.garage_door`
- `lock.8747d40d1654e0ff1e946528135feeaf` → `lock.garage_door`

### 4. Removed Redundant Empty Fields
- Cleaned up `data: {}` and `metadata: {}` fields throughout the file
- Makes code cleaner and more maintainable

### 5. Added Meaningful Descriptions to All Automations
All automations now have descriptive text explaining their purpose:
- "Activates living room evening scene and entryway lamp when family arrives home"
- "Auto-locks side door 20 minutes after being unlocked"
- "Sends periodic notifications with elapsed time when side door is left open"
- etc.

### 6. Enhanced Kitchen Motion Light Automation
**Previous**: Complex system with auto-off and multiple companion automations
**Current**: Simplified automation with manual override support
- Motion trigger: Turns on light when motion detected (if dark and override is off)
- Manual control: Detects manual on/off and sets/clears `input_boolean.auto_light_override`
- No auto-off: Light stays on until manually turned off
- Override respected: Motion won't turn on light if override is active

---

## Key Technical Decisions

### Person Triggers vs Group Triggers
**Decision**: Keep individual person triggers for lock/unlock automations

**Analysis**:
- `group.family` state is "home" when **at least one** person is home
- `group.family` state is "not_home" when **all** persons are away

**Lock Doors Automation**:
- ✅ Current (individual triggers): Locks when **either** person leaves
- ❌ If using group: Would only lock when **both** people leave (last person out)

**Unlock Doors Automation**:
- ✅ Current (individual triggers): Unlocks when **either** person arrives
- ❌ If using group: Would only unlock on first arrival, not if one person already home

**Conclusion**: Individual person triggers are the correct implementation for the intended behavior.

### Kitchen Light Manual Override Pattern
- Uses `input_boolean.auto_light_override` to track manual control
- Manual on/off actions toggle the override boolean
- Motion automation respects override state
- Simpler than previous auto-off system with tracking booleans

---

## Remaining Device IDs to Document

The following device IDs are still in use and may need friendly names or comments:

### Sensors & Devices
- `device_id: 1b838e6a0107f9c966cc779db3fc9396` - Used in gate/door automations (lines 219, 239, 519)
- `device_id: dec89cf186ff751b9b291d485f0a06fb` - Refrigerator sensor (line 255)
- `device_id: 5d0879c339587ed7b1445c2fdb912d0f` - Remote button for basement light (line 273)
- `device_id: 67a94d70b691877adf8fe8da8f6bdb31` - Basement light switch (line 281)
- `device_id: dbbbffb1a9780f1bc4ab76b8eb41dd56` - Washing machine power monitor (lines 349, 372)
- `device_id: 7914c337e4bcb80e0feef9e4cdc52b32` - Dryer vibration sensor (lines 401, 420)
- `device_id: 165e6e79df5e78a6b759625a235bcc15` - Porch lights (lines 474, 481)
- `device_id: a235b93cf4f9b819e0554e6b4062cbf6` - Patio lights switch (line 528)
- `device_id: 64325f8051beb554b767e4e549e81c13` - Kitchen tablet motion sensor (line 542)
- `device_id: 7ed6947d7f1c31e7bb668bbd37c89611` - Side door lock (line 623)

### Entity IDs Still Cryptic
- `entity_id: 6e60ff4f8694be9e0de4370799d590b2` (line 520)
- `entity_id: 3a3ff6e8b6da2c8f2cfc403aaedb12de` (line 529)
- `entity_id: c38727677330bab1e94c53e76156cc62` (line 543)
- `entity_id: e502f8fb1ee7c3fd687bed48d36f85f0` (line 625)

---

## Remaining Tasks / Future Improvements

### High Priority
1. **Add button/toggle automation for kitchen light manual override**
   - Similar to "Time To Cook Toggle" automation (lines 483-512)
   - Should toggle `input_boolean.auto_light_override`
   - Allows easy manual control from dashboard

### Medium Priority
2. **Add comments to document remaining cryptic device IDs**
   - Either replace with friendly entity IDs or add inline comments
   - Helps with future maintenance

3. **Review inconsistent notification targets**
   - Currently using: `notify.family_devices`, `notify.mobile_app_lucy`, `notify.mobile_app_fredrick2`
   - Determine if this is intentional or should be standardized

4. **Review automation modes**
   - Most use `mode: single`
   - Some might benefit from `mode: restart` (especially motion-based automations)

### Low Priority (Optional)
5. **Consider adding presence checks to light automations**
   - Prevent lights turning on when away from home
   - May not be needed if existing logic works well

6. **Review auto-lock timers**
   - Currently 20 minutes for doors
   - May want to adjust for security preferences

---

## Configuration Patterns Established

### Automation Structure
```yaml
- id: 'unique_id'
  alias: 'Area: Descriptive Name'
  description: Clear explanation of what automation does
  triggers:
  - trigger: type
    # trigger config
  conditions:
  - condition: type
    # condition config
  actions:
  - action: service_name
    target:
      entity_id: friendly_name
  mode: single|restart
```

### Naming Conventions
- Use friendly entity IDs (e.g., `lock.garage_door` not `lock.8747d40d...`)
- Remove empty `data: {}` fields
- Keep descriptions concise but informative
- Prefix aliases with area/room when relevant

### Manual Override Pattern
```yaml
# Toggle automation
- trigger: state
  entity_id: input_boolean.feature_toggle
  to: 'on'|'off'
  id: toggle_on|toggle_off
# Actions based on toggle state
```

---

## Files Modified
- `/automations.yaml` - Main automation configuration

## Tools/Integrations Used
- Person entities for presence detection
- Group entities for family presence
- Input booleans for state tracking
- Blueprints for common patterns
- Device triggers for ZHA devices

---

## Notes for Future Development

### When Adding New Automations:
1. Always add meaningful descriptions
2. Use friendly entity IDs (not cryptic device IDs)
3. Consider if individual triggers or group triggers are appropriate
4. Clean up empty `data: {}` fields
5. Choose appropriate mode (single/restart/queued/parallel)

### When Modifying Lock/Unlock Automations:
- Remember: Individual person triggers are intentional
- Don't consolidate to `group.family` without reconsidering behavior
- Current setup locks when first person leaves, unlocks when anyone arrives

### Input Booleans for State Tracking:
- `input_boolean.auto_light_override` - Kitchen light manual override
- `input_boolean.kitchen_light_auto` - No longer used (removed with old automation)
- `input_boolean.after_bedtime` - Bedtime mode toggle
- `input_boolean.washing_machine` - Washing machine running state
- `input_boolean.drying_machine_on` - Dryer running state
- `input_boolean.dishwasher` - Dishwasher running state
- `input_boolean.dishwasher_notification_button` - Dishwasher done notification trigger
- `input_boolean.kitchen_time_to_cook` - Kitchen cooking mode toggle

---

*Last Updated: 2025-10-01*
*Improvements by: Claude Code Assistant*
