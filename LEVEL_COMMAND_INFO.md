# 🎯 Level Command Implementation

## ✅ Successfully Added `/level` Command

### Command Usage
```
/level <teamcode>
```

### What It Does
The `/level` command automates the leveling process by combining 3 existing commands (`!teamcode`, `/start`, `/exit`) into a continuous 30-second loop:

1. **🎯 Join Team** - 1 second delay
2. **🎮 Start Match** - Instant
3. **⏳ Wait** - 28 seconds
4. **🚪 Leave Team** - 1 second delay
5. **🔄 Loop** - Immediately repeats from step 1

**Total Cycle Time: ~30 seconds**

### Commands Added

#### Start Level Loop
```
/level ABC123
```
Replace `ABC123` with your actual team code.

#### Stop Level Loop
```
/level_stop
```

### Example Usage

**In-game chat:**
```
/level XYZ789
```

**Bot Response:**
```
✅ LEVEL LOOP STARTED!

Team Code: XYZ789

Cycle Pattern:
🎯 Join team - 1 second
🎮 Start match - instant
⏳ Wait - 28 seconds
🚪 Leave team - 1 second
🔄 Loop - immediately back to join

Total Cycle: ~30 seconds

To stop: /level_stop
```

### Implementation Details

#### Files Modified
- `main.py` - Added level loop functionality

#### Changes Made

1. **Global Variables Added** (Line ~73-76):
   ```python
   level_running = False
   level_task = None
   level_team_code = None
   ```

2. **Level Loop Function Added** (After `lag_team_loop` function):
   ```python
   async def level_loop(team_code, key, iv, region):
       """Level up loop: Join team -> Start match -> Wait 28s -> Leave -> Repeat every 30s"""
   ```

3. **Command Handlers Added** (After `/start` and `/exit` commands):
   - `/level <teamcode>` - Starts the level loop
   - `/level_stop` - Stops the level loop

4. **Help Menu Updated**:
   - Added `/level [code]` to command list
   - Added `/level_stop` to command list

### Technical Flow

```
┌─────────────────────────────────────┐
│  User sends: /level ABC123          │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  Bot starts level_loop task         │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  CYCLE START (Cycle #1)             │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  1. Join team (GenJoinSquadsPacket) │
│     Wait 1 second                   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  2. Start match (start_auto_packet) │
│     Instant                         │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  3. Wait 28 seconds                 │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  4. Leave team (ExiT)               │
│     Wait 1 second                   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  CYCLE COMPLETE (~30 seconds)       │
│  Immediately loop back to start     │
└──────────────┬──────────────────────┘
               │
               ▼
         (Repeat until stopped)
```

### Console Output Example

```
🎯 Level loop started for team code: ABC123

🔄 Level Cycle #1 starting...
🎯 Joining team: ABC123
🎮 Starting match...
⏳ Waiting 28 seconds...
🚪 Leaving team...
✅ Level Cycle #1 completed! (Total: ~30 seconds)

🔄 Level Cycle #2 starting...
🎯 Joining team: ABC123
🎮 Starting match...
⏳ Waiting 28 seconds...
🚪 Leaving team...
✅ Level Cycle #2 completed! (Total: ~30 seconds)

...

🛑 Level loop stopped after 5 cycles
```

### Error Handling

The loop includes error handling:
- If any step fails, it waits 2 seconds and continues
- Loop can be safely stopped with `/level_stop`
- Only one level loop can run at a time (new loop stops old one)

### Safety Features

1. **Single Instance**: Starting a new `/level` command automatically stops any existing level loop
2. **Clean Stop**: `/level_stop` properly cancels the task and cleans up
3. **Error Recovery**: Continues running even if individual cycles fail
4. **Console Logging**: All actions are logged for debugging

## 🎮 How to Use

1. Join a squad/team in Free Fire
2. Send command in squad chat: `/level ABC123` (replace with your team code)
3. Bot will automatically:
   - Join the team
   - Start match
   - Wait 28 seconds
   - Leave team
   - Repeat every 30 seconds
4. To stop: Send `/level_stop`

## 📝 Notes

- The command works in any chat type (squad, guild, whisper)
- Each cycle takes approximately 30 seconds
- The loop runs continuously until stopped
- You can see progress in the console/terminal
- Added to help menu for easy discovery

## ✨ Features

- ✅ Combines 3 commands into 1
- ✅ Automatic 30-second loop
- ✅ Easy start/stop controls
- ✅ Console logging for monitoring
- ✅ Error handling and recovery
- ✅ Clean task management
- ✅ Added to help menu
- ✅ Works in all chat types
