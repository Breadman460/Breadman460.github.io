<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Etaf Language Sandbox</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f7f9fc;
            color: #333;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        .header {
            text-align: center;
            border-bottom: 2px solid #ddd;
            padding-bottom: 15px;
        }
        .workspace {
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
        }
        .editor-container, .output-container {
            flex: 1;
            min-width: 300px;
        }
        .editor-container h2, .output-container h2 {
            margin-top: 0;
            color: #2c3e50;
        }
        #editor {
            width: 100%;
            height: 400px;
            font-family: monospace;
            font-size: 16px;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 5px;
            background-color: #fff;
            resize: vertical;
        }
        #output {
            width: 100%;
            height: 250px;
            font-family: monospace;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 5px;
            background-color: #fff;
            overflow-y: auto;
            margin-bottom: 15px;
        }
        #display-container {
            width: 100%;
            overflow: auto;
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 5px;
            background-color: #fff;
        }
        #display-grid {
            border-collapse: collapse;
            margin: 0 auto;
        }
        #display-grid td {
            width: 20px;
            height: 20px;
            border: 1px solid #eee;
        }
        button {
            padding: 10px 15px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #45a049;
        }
        .cheat-sheet {
            padding: 15px;
            background-color: #fff;
            border: 2px solid #ddd;
            border-radius: 5px;
            margin-top: 20px;
        }
        .cheat-sheet h3 {
            margin-top: 0;
            color: #2c3e50;
        }
        .cheat-sheet pre {
            background-color: #f5f5f5;
            padding: 10px;
            border-radius: 4px;
            overflow-x: auto;
        }
        .error {
            color: red;
            font-weight: bold;
        }
        .memory-display {
            margin-top: 15px;
            padding: 10px;
            background-color: #f5f5f5;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Etaf Language Sandbox</h1>
            <p>Write and execute Etaf code in this interactive environment</p>
        </div>
        
        <div class="workspace">
            <div class="editor-container">
                <h2>Code Editor</h2>
                <textarea id="editor" placeholder="Enter your Etaf code here...">{ Note: "Hello World Example" }
{ Raw: "Welcome to Etaf!" }

{ {Func:Save} [Text] [Hello from Etaf!] [greeting] }
{ {Func:Load} [Text] [greeting] }

{ {Math:Add} [2] [3] [result] }
{ {Func:Load} [Text] [result] }

{ {Display:} [5] [10] [Grid:Show=1] [Pixel:2,3=Red] [Pixel:2,4=Blue] [Pixel:2,5=Green] }</textarea>
                <div style="margin-top: 10px;">
                    <button id="run-btn">Run Code</button>
                    <button id="clear-btn">Clear Output</button>
                    <button id="show-memory-btn">Show Memory</button>
                </div>
            </div>
            
            <div class="output-container">
                <h2>Output</h2>
                <div id="output"></div>
                <div id="memory-display" class="memory-display" style="display: none;">
                    <h3>Memory Contents</h3>
                    <pre id="memory-content"></pre>
                </div>
                <h2>Display Grid</h2>
                <div id="display-container">
                    <table id="display-grid"></table>
                </div>
            </div>
        </div>
        
        <div class="cheat-sheet">
            <h3>Etaf Cheat Sheet</h3>
            <pre>
Core Nodes:
{ ... } - Delimiters for all Etaf nodes
{ Note: "..." } - Annotation/comment
{ Raw: [Content] } - Output raw content

Memory:
{ {Func:Save} [Type] [Data] [MemoryLocation] } - Save data to memory
{ {Func:Load} [Type] [MemoryLocation] } - Load data from memory

Math:
{ {Math:Add} [Value1] [Value2] [ResultVariable] } - Adds two numbers and saves result
{ {Math:Subtract} [Value1] [Value2] [ResultVariable] } - Subtracts and saves result
{ {Math:Multiply} [Value1] [Value2] [ResultVariable] } - Multiplies and saves result
{ {Math:Divide} [Value1] [Value2] [ResultVariable] } - Divides and saves result

String:
{ {Func:Concatenate} [String1] [String2] [ResultVariable] }
{ {Func:Substring} [String] [StartIndex] [Length] [ResultVariable] }

Control Flow:
{ {If} [ConditionVariable] { ...nodes... } }
{ {Else} { ...nodes... } }
{ {Loop} [CountVariable] { ...nodes... } }
{ {While} [ConditionVariable] { ...nodes... } }

Display:
{ {Display:} [Height] [Width] [Sub-Tag1] [Sub-Tag2] ... }
Grid:Show=[0|1]
Pixel:[Row],[Column]=<Color>
            </pre>
        </div>
    </div>

    <script>
        // Memory storage for the Etaf language
        const memory = {};
        let output = document.getElementById('output');
        const displayGrid = document.getElementById('display-grid');
        const memoryDisplay = document.getElementById('memory-display');
        const memoryContent = document.getElementById('memory-content');
        
        // Run button click handler
        document.getElementById('run-btn').addEventListener('click', function() {
            runEtafCode();
        });
        
        // Clear button click handler
        document.getElementById('clear-btn').addEventListener('click', function() {
            output.innerHTML = '';
            displayGrid.innerHTML = '';
        });
        
        // Show Memory button click handler
        document.getElementById('show-memory-btn').addEventListener('click', function() {
            updateMemoryDisplay();
            if (memoryDisplay.style.display === 'none') {
                memoryDisplay.style.display = 'block';
                this.textContent = 'Hide Memory';
            } else {
                memoryDisplay.style.display = 'none';
                this.textContent = 'Show Memory';
            }
        });

        function updateMemoryDisplay() {
            memoryContent.textContent = JSON.stringify(memory, null, 2);
        }

        function runEtafCode() {
            const code = document.getElementById('editor').value;
            output.innerHTML = '';
            
            try {
                // Parse and execute Etaf code
                const tokens = tokenizeEtaf(code);
                executeEtaf(tokens);
                updateMemoryDisplay();
            } catch (error) {
                output.innerHTML += `<div class="error">Error: ${error.message}</div>`;
            }
        }

        function tokenizeEtaf(code) {
            const tokens = [];
            let i = 0;
            
            while (i < code.length) {
                // Skip whitespace
                if (/\s/.test(code[i])) {
                    i++;
                    continue;
                }
                
                // Start of a node
                if (code[i] === '{') {
                    let depth = 1;
                    let nodeContent = '';
                    i++; // Skip the opening brace
                    
                    // Extract content until matching closing brace
                    while (i < code.length && depth > 0) {
                        if (code[i] === '{') depth++;
                        else if (code[i] === '}') depth--;
                        
                        if (depth > 0 || code[i] !== '}') {
                            nodeContent += code[i];
                        }
                        i++;
                    }
                    
                    tokens.push({ type: 'node', content: nodeContent.trim() });
                } else {
                    // Unexpected character
                    i++;
                }
            }
            
            return tokens;
        }

        function executeEtaf(tokens) {
            for (const token of tokens) {
                if (token.type === 'node') {
                    executeNode(token.content);
                }
            }
        }

        function executeNode(nodeContent) {
            // Debug info to help trace execution
            console.log("Executing node:", nodeContent);
            
            // Handle Note node
            if (nodeContent.startsWith('Note:')) {
                // Notes are just comments, no execution needed
                return;
            }
            
            // Handle Raw node
            if (nodeContent.startsWith('Raw:')) {
                const rawContent = nodeContent.substring(4).trim();
                output.innerHTML += `<div>${rawContent}</div>`;
                return;
            }
            
            // Handle Func:Save - FIXED
            if (nodeContent.includes('{Func:Save}')) {
                // Updated regex pattern to properly capture all three parameters
                const saveRegex = /\{Func:Save\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(saveRegex);
                
                if (match) {
                    const type = match[1].trim();
                    const data = match[2].trim();
                    const location = match[3].trim();
                    
                    console.log("Saving to memory:", type, data, location);
                    
                    memory[location] = {
                        type: type,
                        data: data
                    };
                    
                    // Confirm the save operation in output
                    output.innerHTML += `<div>Saved ${type} to memory location "${location}"</div>`;
                } else {
                    output.innerHTML += `<div class="error">Error: Invalid Save syntax</div>`;
                }
                return;
            }
            
            // Handle Func:Load - FIXED
            if (nodeContent.includes('{Func:Load}')) {
                const loadRegex = /\{Func:Load\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(loadRegex);
                
                if (match) {
                    const type = match[1].trim();
                    const location = match[2].trim();
                    
                    console.log("Loading from memory:", type, location, memory[location]);
                    
                    if (memory[location] && memory[location].type === type) {
                        if (type === 'Code') {
                            output.innerHTML += `<div>Executing code from memory location "${location}"</div>`;
                            // Execute the loaded code
                            executeNode(memory[location].data);
                        } else {
                            // Just display the data for other types
                            output.innerHTML += `<div>${memory[location].data}</div>`;
                        }
                    } else {
                        output.innerHTML += `<div class="error">Error: Memory location "${location}" not found or type mismatch</div>`;
                    }
                } else {
                    output.innerHTML += `<div class="error">Error: Invalid Load syntax</div>`;
                }
                return;
            }
            
            // Handle Math operations - FIXED
            if (nodeContent.includes('{Math:')) {
                handleMathOperation(nodeContent);
                return;
            }
            
            // Handle String operations
            if (nodeContent.includes('{Func:Concatenate}') || nodeContent.includes('{Func:Substring}')) {
                handleStringOperation(nodeContent);
                return;
            }
            
            // Handle Display node
            if (nodeContent.includes('Display:}')) {
                handleDisplayNode(nodeContent);
                return;
            }
            
            // Handle comparison operations
            if (nodeContent.includes('{Func:Equals}') || 
                nodeContent.includes('{Func:GreaterThan}') || 
                nodeContent.includes('{Func:LessThan}')) {
                handleComparisonOperation(nodeContent);
                return;
            }
            
            // Handle If/Else control flow
            if (nodeContent.includes('{If}')) {
                // For simplicity, this implementation is partial
                const ifRegex = /\{If\}\s*\[([^\]]+)\]\s*\{(.+?)\}/;
                const match = nodeContent.match(ifRegex);
                
                if (match) {
                    const conditionVar = match[1].trim();
                    const innerNodes = match[2];
                    
                    if (memory[conditionVar] && memory[conditionVar].data === 'true') {
                        // Execute inner nodes
                        executeNode(innerNodes);
                    }
                }
                return;
            }
            
            // Handle Loop
            if (nodeContent.includes('{Loop}')) {
                const loopRegex = /\{Loop\}\s*\[(\d+)\]\s*\{(.+?)\}/;
                const match = nodeContent.match(loopRegex);
                
                if (match) {
                    const count = parseInt(match[1]);
                    const innerNodes = match[2];
                    
                    for (let i = 0; i < count; i++) {
                        executeNode(innerNodes);
                    }
                }
                return;
            }
        }

        function handleMathOperation(nodeContent) {
            // Handle Math:Add - FIXED
            if (nodeContent.includes('{Math:Add}')) {
                const regex = /\{Math:Add\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = parseFloat(match[1].trim());
                    const value2 = parseFloat(match[2].trim());
                    const resultVar = match[3].trim();
                    
                    if (!isNaN(value1) && !isNaN(value2)) {
                        const result = value1 + value2;
                        
                        // Save result to memory
                        memory[resultVar] = {
                            type: 'Text',
                            data: result.toString()
                        };
                        
                        output.innerHTML += `<div>Result of ${value1} + ${value2} = ${result} (saved to ${resultVar})</div>`;
                    } else {
                        output.innerHTML += `<div class="error">Error: Invalid numbers for addition</div>`;
                    }
                } else {
                    output.innerHTML += `<div class="error">Error: Invalid Math:Add syntax</div>`;
                }
                return;
            }
            
            // Handle Math:Subtract - FIXED
            if (nodeContent.includes('{Math:Subtract}')) {
                const regex = /\{Math:Subtract\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = parseFloat(match[1].trim());
                    const value2 = parseFloat(match[2].trim());
                    const resultVar = match[3].trim();
                    
                    if (!isNaN(value1) && !isNaN(value2)) {
                        const result = value1 - value2;
                        
                        // Save result to memory
                        memory[resultVar] = {
                            type: 'Text',
                            data: result.toString()
                        };
                        
                        output.innerHTML += `<div>Result of ${value1} - ${value2} = ${result} (saved to ${resultVar})</div>`;
                    } else {
                        output.innerHTML += `<div class="error">Error: Invalid numbers for subtraction</div>`;
                    }
                }
                return;
            }
            
            // Handle Math:Multiply - FIXED
            if (nodeContent.includes('{Math:Multiply}')) {
                const regex = /\{Math:Multiply\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = parseFloat(match[1].trim());
                    const value2 = parseFloat(match[2].trim());
                    const resultVar = match[3].trim();
                    
                    if (!isNaN(value1) && !isNaN(value2)) {
                        const result = value1 * value2;
                        
                        // Save result to memory
                        memory[resultVar] = {
                            type: 'Text',
                            data: result.toString()
                        };
                        
                        output.innerHTML += `<div>Result of ${value1} * ${value2} = ${result} (saved to ${resultVar})</div>`;
                    } else {
                        output.innerHTML += `<div class="error">Error: Invalid numbers for multiplication</div>`;
                    }
                }
                return;
            }
            
            // Handle Math:Divide - FIXED
            if (nodeContent.includes('{Math:Divide}')) {
                const regex = /\{Math:Divide\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = parseFloat(match[1].trim());
                    const value2 = parseFloat(match[2].trim());
                    const resultVar = match[3].trim();
                    
                    if (!isNaN(value1) && !isNaN(value2) && value2 !== 0) {
                        const result = value1 / value2;
                        
                        // Save result to memory
                        memory[resultVar] = {
                            type: 'Text',
                            data: result.toString()
                        };
                        
                        output.innerHTML += `<div>Result of ${value1} / ${value2} = ${result} (saved to ${resultVar})</div>`;
                    } else {
                        output.innerHTML += `<div class="error">Error: Division by zero or invalid numbers</div>`;
                    }
                }
                return;
            }
        }

        function handleStringOperation(nodeContent) {
            // Handle Func:Concatenate - FIXED
            if (nodeContent.includes('{Func:Concatenate}')) {
                const regex = /\{Func:Concatenate\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const string1 = match[1].trim();
                    const string2 = match[2].trim();
                    const resultVar = match[3].trim();
                    
                    memory[resultVar] = {
                        type: 'Text',
                        data: string1 + string2
                    };
                    
                    output.innerHTML += `<div>Concatenated "${string1}" and "${string2}" (saved to ${resultVar})</div>`;
                }
                return;
            }
            
            // Handle Func:Substring - FIXED
            if (nodeContent.includes('{Func:Substring}')) {
                const regex = /\{Func:Substring\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const string = match[1].trim();
                    const startIndex = parseInt(match[2].trim());
                    const length = parseInt(match[3].trim());
                    const resultVar = match[4].trim();
                    
                    if (!isNaN(startIndex) && !isNaN(length) && startIndex >= 0 && length >= 0) {
                        const result = string.substring(startIndex, startIndex + length);
                        
                        memory[resultVar] = {
                            type: 'Text',
                            data: result
                        };
                        
                        output.innerHTML += `<div>Extracted substring from "${string}" (saved to ${resultVar})</div>`;
                    }
                }
                return;
            }
        }

        function handleComparisonOperation(nodeContent) {
            // Handle Func:Equals - FIXED
            if (nodeContent.includes('{Func:Equals}')) {
                const regex = /\{Func:Equals\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = match[1].trim();
                    const value2 = match[2].trim();
                    const resultVar = match[3].trim();
                    
                    memory[resultVar] = {
                        type: 'Boolean',
                        data: (value1 === value2) ? 'true' : 'false'
                    };
                    
                    output.innerHTML += `<div>Compared "${value1}" and "${value2}" (result saved to ${resultVar})</div>`;
                }
                return;
            }
            
            // Handle Func:GreaterThan - FIXED
            if (nodeContent.includes('{Func:GreaterThan}')) {
                const regex = /\{Func:GreaterThan\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = parseFloat(match[1].trim());
                    const value2 = parseFloat(match[2].trim());
                    const resultVar = match[3].trim();
                    
                    if (!isNaN(value1) && !isNaN(value2)) {
                        memory[resultVar] = {
                            type: 'Boolean',
                            data: (value1 > value2) ? 'true' : 'false'
                        };
                        
                        output.innerHTML += `<div>Compared ${value1} > ${value2} (result saved to ${resultVar})</div>`;
                    }
                }
                return;
            }
            
            // Handle Func:LessThan - FIXED
            if (nodeContent.includes('{Func:LessThan}')) {
                const regex = /\{Func:LessThan\}\s*\[([^\]]+)\]\s*\[([^\]]+)\]\s*\[([^\]]+)\]/;
                const match = nodeContent.match(regex);
                
                if (match) {
                    const value1 = parseFloat(match[1].trim());
                    const value2 = parseFloat(match[2].trim());
                    const resultVar = match[3].trim();
                    
                    if (!isNaN(value1) && !isNaN(value2)) {
                        memory[resultVar] = {
                            type: 'Boolean',
                            data: (value1 < value2) ? 'true' : 'false'
                        };
                        
                        output.innerHTML += `<div>Compared ${value1} < ${value2} (result saved to ${resultVar})</div>`;
                    }
                }
                return;
            }
        }

        function handleDisplayNode(nodeContent) {
            const displayRegex = /\{Display:\}\s*\[(\d+)\]\s*\[(\d+)\]/;
            const match = nodeContent.match(displayRegex);
            
            if (match) {
                const height = parseInt(match[1]);
                const width = parseInt(match[2]);
                
                // Create display grid
                displayGrid.innerHTML = '';
                const showGrid = nodeContent.includes('Grid:Show=1');
                
                for (let row = 0; row < height; row++) {
                    const tr = document.createElement('tr');
                    for (let col = 0; col < width; col++) {
                        const td = document.createElement('td');
                        if (showGrid) {
                            td.style.border = '1px solid #ccc';
                        } else {
                            td.style.border = 'none';
                        }
                        tr.appendChild(td);
                    }
                    displayGrid.appendChild(tr);
                }
                
                // Handle pixel coloring
                const pixelRegex = /Pixel:(\d+),(\d+)=(\w+)/g;
                let pixelMatch;
                
                while ((pixelMatch = pixelRegex.exec(nodeContent)) !== null) {
                    const row = parseInt(pixelMatch[1]);
                    const col = parseInt(pixelMatch[2]);
                    const color = pixelMatch[3];
                    
                    if (row < height && col < width) {
                        displayGrid.rows[row].cells[col].style.backgroundColor = color;
                    }
                }
            }
        }
    </script>
</body>
</html>
