# Google Sheets Loader for GameMaker

A powerful, type-intelligent CSV loader for GameMaker that fetches data directly from published Google Sheets and automatically parses it into native GameMaker data types including strings, numbers, arrays, structs, and **executable functions**.

## 🚀 Features

- **📊 Direct Google Sheets Integration** - Load data from published Google Sheets using simple Sheet IDs
- **🧠 Intelligent Type Detection** - Automatically converts CSV strings into appropriate GameMaker types
- **🔧 Advanced Data Types Support**:
  - ✅ Strings
  - ✅ Numbers (integers and floats)
  - ✅ Arrays `[1, 2, 3]`
  - ✅ Structs/Objects `{key: "value"}`
  - ✅ **Executable Functions** `callback("param1", 123)` - *Parse and execute functions directly from your spreadsheet!*
- **🐛 Built-in Debug Tools** - Comprehensive debugging functions to visualize your data
- **⚡ Async Loading** - Non-blocking HTTP requests for smooth gameplay
- **🎯 Tab Support** - Load specific tabs/sheets using GID
- **📝 CSV Compliant** - Handles quoted fields, escaped characters, and complex data

## 🎮 Why Use This?

### Traditional Approach (Hard-coded):
```gml
// Manually create all your game data
items[0] = {name: "Banana", color: "yellow", price: 12};
items[1] = {name: "Apple", color: "red", price: 10};
items[2] = {name: "Orange", color: "orange", price: 15};
// ... tedious and inflexible
```

### With Google Sheets Loader:
```gml
// Just load from a spreadsheet anyone can edit!
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    global.items = data;  // Done! Fully typed and ready to use
});
```

**Perfect for:**
- 🎲 Game databases (items, enemies, levels, dialogue)
- 🌍 Localization/translation tables
- ⚙️ Configuration and balancing data
- 📊 Analytics and testing data
- 🔄 Live content updates without recompiling
- 🤝 Non-programmer friendly data management

## 📦 Installation

### Step 1: Import the Package

1. Download the `GMGSL.yymps` file from the [Releases](../../releases) page
2. In GameMaker, go to **Tools → Import Local Package**
3. Select the downloaded `GMGSL.yymps` file
4. In the import dialog, select **obj_GMGSL** (or click **Add All**)
5. Click **Import**

That's it! The object is now in your Asset Browser, ready to use.

**✨ No coding required!** The object comes pre-configured with all the parsing logic, debug functions, and utilities built-in.

### Step 2: Add to Your Room

1. Find **obj_GMGSL** in your Asset Browser (under Objects)
2. **Drag and drop** it into any room in your game
3. The object persists automatically, so you only need one instance

### Step 3: Publish Your Google Sheet

1. Open your Google Sheet
2. Go to **File → Share → Publish to web**
3. Choose **Entire Document** or specific sheet
4. **IMPORTANT:** Select **"Web page"** from the format dropdown (NOT "Comma-separated values")
5. Click **Publish**
6. Copy the published URL or extract the Sheet ID

**Why Web page and not CSV?**  
GameMaker's HTTP functions can access the web page format and automatically convert it to CSV internally. Direct CSV publishing is not accessible via `http_get()`.

**Sheet ID Format:**
- Full URL: `https://docs.google.com/spreadsheets/d/e/2PACX-1vRo...E2AIN/pub?output=csv`
- Sheet ID: `2PACX-1vRo...E2AIN` (the part between `/d/e/` and `/pub`)

### Step 4: Start Loading Data!

You're ready to go! Just call the load function from any object's event:

```gml
// Example: Load on game start (in a controller's Create Event)
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    global.items = data;
    show_debug_message("Loaded " + string(array_length(data)) + " items!");
});
```

**That's it!** No setup code needed - everything is built into the object.

## 🚀 Quick Start Example

Here's a complete example to get you started immediately:

**1. Create this simple Google Sheet:**

| name   | price | description        |
|--------|-------|--------------------|
| Sword  | 100   | A sharp blade      |
| Shield | 50    | Protects from harm |
| Potion | 25    | Restores health    |

**2. Publish it as CSV (File → Share → Publish to web → CSV)**

**3. In any GameMaker object, add this to a Create Event or Key Press:**

```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    // Debug: See what you loaded
    _gmgsl_debug_sheet_data_compact(data);
    
    // Use the data
    for (var i = 0; i < array_length(data); i++) {
        var item = data[i];
        show_debug_message(item.name + " costs " + string(item.price) + " gold");
    }
});
```

**Done!** You're now loading data from Google Sheets into your game.

## 🎯 Usage

### Basic Loading

```gml
// Load entire sheet
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    // data is an array of structs, one per row
    show_debug_message("Loaded " + string(array_length(data)) + " items");
    
    // Access data by column name
    var first_item = data[0];
    show_debug_message("First item: " + first_item.name);
});
```

### Load Specific Tab

```gml
// Load a specific tab using its GID (found in the tab URL: gid=123456)
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "123456", function(data) {
    // Process specific tab data
});
```

### Example Google Sheet Structure

| name    | color  | type      | price | stock | tags              | metadata                    |
|---------|--------|-----------|-------|-------|-------------------|-----------------------------|
| Banana  | yellow | fruit     | 12    | 100   | [0, 1, 2]         | {origin: "Ecuador"}         |
| Apple   | red    | fruit     | 10    | 50    | ["fresh", "sweet"]| {origin: "USA"}             |
| Carrot  | orange | vegetable | 5     | 200   | [1, 2]            | {origin: "Netherlands"}     |

### Access Parsed Data

```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    var item = data[0];
    
    // Strings
    show_debug_message(item.name);    // "Banana"
    show_debug_message(item.color);   // "yellow"
    
    // Numbers (automatically converted)
    var total_value = item.price * item.stock;  // 12 * 100 = 1200
    
    // Arrays (automatically parsed)
    if (is_array(item.tags)) {
        for (var i = 0; i < array_length(item.tags); i++) {
            show_debug_message("Tag: " + string(item.tags[i]));
        }
    }
    
    // Structs (automatically parsed)
    if (is_struct(item.metadata)) {
        show_debug_message("Origin: " + item.metadata.origin);
    }
});
```

### Executable Functions 🔥

One of the most powerful features - execute functions directly from your spreadsheet!

**Note:** Functions support up to **8 parameters**. If you need more, pass a struct or array as a parameter.

**Google Sheet:**

| name    | callback                          |
|---------|-----------------------------------|
| Banana  | on_collect("banana", 12)          |
| Apple   | on_collect("apple", 10)           |
| Powerup | activate_powerup("speed", 5.0)    |

**GameMaker Script (create as Script asset):**

```gml
/// @function on_collect(item_name, points)
function on_collect(_item_name, _points) {
    show_debug_message("Collected: " + _item_name);
    global.score += _points;
    return true;
}
```

**Usage:**

```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    var item = data[0];
    
    // Check if callback is a function
    if (_gmgsl_is_function_object(item.callback)) {
        // Call it with CSV parameters
        item.callback.call();
        
        // Or override parameters
        item.callback.call(["custom_item", 999]);
        
        // Access function details
        show_debug_message("Function: " + item.callback.name);
        show_debug_message("Params: " + string(item.callback.param_count));
    }
});
```

### Store Data Globally

```gml
// Load once at game start
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    global.game_items = data;
    global.data_loaded = true;
});

// Access anywhere in your game
if (global.data_loaded) {
    var item = global.game_items[0];
    instance_create_layer(x, y, "Instances", obj_item, {
        sprite_index: asset_get_index("spr_" + item.name),
        item_data: item
    });
}
```

## 🛠️ Utility Functions

### Debug Functions

#### `_gmgsl_debug_sheet_data(data)`

Displays complete, detailed output of all loaded data with full type information.

**Input:**
- `data` (array): The data array returned from `load_sheet` callback

**Output:** Console debug messages with formatted data display

**Example:**
```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    _gmgsl_debug_sheet_data(data);
});
```

**Console Output:**
```
╔════════════════════════════════════════════════════════════════╗
║           GOOGLE SHEETS DATA - DEBUG OUTPUT                    ║
╠════════════════════════════════════════════════════════════════╣
║  Total Rows: 3                                                 ║
╚════════════════════════════════════════════════════════════════╝

┌─ Row 0 ──────────────────────────────────────────────────────
│
├─ Column: name
  "Banana" (string)
│
├─ Column: price
  12 (number)
│
├─ Column: tags
  Array [3 elements]:
    [0]:
      0 (number)
    [1]:
      1 (number)
    [2]:
      2 (number)
│
├─ Column: callback
  Function: on_collect()
    Parameters: 2
      [0]: banana (string)
      [1]: 12 (number)
    Original: on_collect("banana", 12)
    (callable)
└───────────────────────────────────────────────────────────────
```

---

#### `_gmgsl_debug_sheet_data_compact(data)`

Shows data in a compact, one-line-per-row format. Perfect for quick verification.

**Input:**
- `data` (array): The data array returned from `load_sheet` callback

**Output:** Console debug messages with compact format

**Example:**
```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    _gmgsl_debug_sheet_data_compact(data);
});
```

**Console Output:**
```
=== GOOGLE SHEETS DATA (3 rows) ===
Row 0: { name: "Banana", price: 12, tags: [3 items], callback: on_collect() [function] }
Row 1: { name: "Apple", price: 10, tags: [2 items], callback: on_collect() [function] }
Row 2: { name: "Orange", price: 15, tags: [4 items], callback: on_collect() [function] }
=== END OF DATA ===
```

---

#### `_gmgsl_debug_sheet_data_summary(data)`

Shows a statistical summary of the loaded data including column names and types.

**Input:**
- `data` (array): The data array returned from `load_sheet` callback

**Output:** Console debug messages with summary statistics

**Example:**
```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    _gmgsl_debug_sheet_data_summary(data);
});
```

**Console Output:**
```
╔════════════════════════════════════════╗
║      DATA SUMMARY                      ║
╠════════════════════════════════════════╣
║  Total Rows: 3                         ║
║  Columns: 4                            ║
╠════════════════════════════════════════╣
║  Column Names:                         ║
║    1. name                             ║
║    2. price                            ║
║    3. tags                             ║
║    4. callback                         ║
╠════════════════════════════════════════╣
║  Type Analysis:                        ║
║    name: string                        ║
║    price: number                       ║
║    tags: array                         ║
║    callback: function                  ║
╚════════════════════════════════════════╝
```

---

#### `_gmgsl_debug_call_all_functions(data)`

Finds and executes all function objects in the data. Useful for testing callbacks.

**Input:**
- `data` (array): The data array returned from `load_sheet` callback

**Output:** 
- Console debug messages showing execution results
- All detected functions are called with their stored parameters

**Example:**
```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    _gmgsl_debug_call_all_functions(data);
});
```

**Console Output:**
```
╔════════════════════════════════════════╗
║      CALLING ALL FUNCTIONS             ║
╚════════════════════════════════════════╝

┌─ Calling: on_collect()
│  From row: 0, column: callback
│  Parameters: 2
│  Result: true
└─ Done

┌─ Calling: on_collect()
│  From row: 1, column: callback
│  Parameters: 2
│  Result: true
└─ Done

╔════════════════════════════════════════╗
║  Total functions called: 2             ║
╚════════════════════════════════════════╝
```

---

#### `_gmgsl_debug_display_data(data, [indent_level])`

Low-level recursive function for displaying any data structure. Can be used standalone.

**Input:**
- `data` (any): Any data to display (struct, array, string, number, etc.)
- `indent_level` (real, optional): Internal indentation level (default: 0)

**Output:** Console debug messages with hierarchical structure

**Example:**
```gml
// Display a specific item
var item = data[0];
_gmgsl_debug_display_data(item, 0);

// Display just an array
_gmgsl_debug_display_data(item.tags, 0);

// Display nested structures
_gmgsl_debug_display_data({
    player: "John",
    inventory: [1, 2, 3],
    stats: {hp: 100, mp: 50}
}, 0);
```

---

### Type Checking Functions

#### `_gmgsl_is_function_object(value)`

Checks if a value is a parsed function object.

**Input:**
- `value` (any): Value to check

**Returns:** `true` if value is a function object, `false` otherwise

**Example:**
```gml
if (_gmgsl_is_function_object(data[0].callback)) {
    show_debug_message("It's a function!");
    data[0].callback.call();
}
```

---

## 📋 Data Type Reference

### Strings
**CSV:** `Banana` or `"Hello World"`  
**GameMaker:** `"Banana"` (string)

### Numbers
**CSV:** `42` or `3.14` or `-10`  
**GameMaker:** `42` or `3.14` or `-10` (real)

### Arrays
**CSV:** `[1, 2, 3]` or `["a", "b", "c"]` or `[1, "mixed", 3]`  
**GameMaker:** `[1, 2, 3]` (array)

**Note:** Mixed-type arrays are supported!

### Structs/Objects
**CSV:** `{key: "value", count: 10}`  
**GameMaker:** `{key: "value", count: 10}` (struct)

### Functions
**CSV:** `function_name("param1", 123, true)`  
**GameMaker:** Function object with callable `call()` method

**Properties:**
- `.name` - Function name (string)
- `.params` - Array of parameters (array)
- `.param_count` - Number of parameters (real)
- `.original_string` - Original CSV string (string)
- `.call([override_params])` - Method to execute function

## ⚙️ Advanced Usage

### Error Handling

```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    if (data == undefined) {
        show_debug_message("Failed to load data!");
        // Handle error - show message, retry, use cached data, etc.
        return;
    }
    
    // Process data
    global.items = data;
});
```

### Multiple Sheets

```gml
// Load multiple sheets
obj_GMGSL.load_sheet("SHEET_ID_1", "", function(data) {
    global.items = data;
});

obj_GMGSL.load_sheet("SHEET_ID_2", "", function(data) {
    global.enemies = data;
});

obj_GMGSL.load_sheet("SHEET_ID_3", "123456", function(data) {
    global.localization = data;
});
```

### Data Validation

```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    // Validate data structure
    if (array_length(data) == 0) {
        show_debug_message("WARNING: No data loaded!");
        return;
    }
    
    // Validate required columns
    var first_row = data[0];
    if (!variable_struct_exists(first_row, "name") || 
        !variable_struct_exists(first_row, "price")) {
        show_debug_message("ERROR: Missing required columns!");
        return;
    }
    
    // Validate data types
    for (var i = 0; i < array_length(data); i++) {
        if (!is_real(data[i].price)) {
            show_debug_message("WARNING: Invalid price at row " + string(i));
        }
    }
    
    global.items = data;
});
```

### Custom Function Parameters

```gml
obj_GMGSL.load_sheet("YOUR_SHEET_ID", "", function(data) {
    var item = data[0];
    
    if (_gmgsl_is_function_object(item.callback)) {
        // Use original parameters from CSV
        item.callback.call();
        
        // Override with custom parameters
        item.callback.call(["new_item", 999]);
        
        // Modify stored parameters
        var params = item.callback.params;
        params[1] = params[1] * 2;  // Double the second parameter
        item.callback.call(params);
    }
});
```

## 🐛 Troubleshooting

### "Function not found" Error

**Problem:** `WARNING: Function 'my_function' not found!`

**Solution:** The function must be accessible via `asset_get_index()`. The easiest way is to create it as a **Script asset**.

**Option 1: Script Asset (Recommended)**
```gml
// Create a Script asset named "my_function"
function my_function(_param) {
    show_debug_message("Called with: " + string(_param));
}
```

**Option 2: Global Function**
```gml
// Define it globally before loading the sheet
global.my_function = function(_param) {
    show_debug_message("Called with: " + string(_param));
}
```

**Note:** Inline functions defined in events won't work unless stored globally or as Script assets.

**Parameter Limit:** Functions support up to **8 parameters**. If you need more, consider passing a struct or array as a single parameter.

### Data Not Loading

1. **Check sheet is published:** File → Share → Publish to web
2. **IMPORTANT - Verify format:** Must publish as **"Web page"**, NOT as "CSV"
   - In the publish dialog, select **"Web page"** from the dropdown
   - GameMaker cannot access sheets published as CSV format
3. **Check console:** Look for HTTP errors or "Request failed" messages
4. **Test Sheet ID:** Make sure you copied the correct ID from the published URL

### Type Detection Issues

If a value isn't parsing as expected:

```gml
// Check what type it became
show_debug_message("Type: " + typeof(data[0].column_name));

// Manually convert if needed
var my_number = real(data[0].text_number);  // Force to number
var my_string = string(data[0].number_text);  // Force to string
```

## 📄 License

MIT License - Feel free to use in your projects!

## 🤝 Contributing

Contributions welcome! Please submit issues and pull requests on GitHub.

## 💡 Tips & Best Practices

1. **Column Names:** Use simple, lowercase names without spaces (use underscores: `item_name`)
2. **Data Validation:** Always validate loaded data before using it
3. **Caching:** Store loaded data globally to avoid repeated HTTP requests
4. **Testing:** Use debug functions during development
5. **Functions:** Keep callback functions simple and create them as separate Script assets
6. **Performance:** Load data at game start, not during gameplay
7. **Versioning:** Keep a backup of your sheet structure

## 📞 Support

If you encounter issues:
1. Check the troubleshooting section
2. Use debug functions to inspect your data
3. Verify your Google Sheet is properly published
4. Open an issue on GitHub with details

---

Made with ❤️ for the GameMaker community in Morocco 🇲🇦 by a French 🇫🇷 developer
