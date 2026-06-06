# Fleur-Salon-Queue
To track turns for technicians daily
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Nail Tech Queue Tracker</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            max-width: 600px;
            margin: 20px auto;
            padding: 0 20px;
            background-color: #f9fafb;
            color: #333;
        }
        h1, h2 { text-align: center; color: #111827; }
        
        /* Add Tech Form */
        .input-group { display: flex; margin-bottom: 20px; }
        .input-group input { 
            flex: 1; padding: 12px; border: 1px solid #d1d5db; 
            border-radius: 6px 0 0 6px; font-size: 16px;
        }
        .input-group button { 
            padding: 12px 20px; background-color: #4f46e5; color: white; 
            border: none; border-radius: 0 6px 6px 0; cursor: pointer; 
            font-weight: bold; font-size: 16px;
        }

        /* Queue Styling */
        .queue-item { 
            display: flex; justify-content: space-between; align-items: center; 
            background: white; padding: 15px; margin-bottom: 10px; 
            border-radius: 8px; border: 1px solid #e5e7eb;
            box-shadow: 0 1px 3px rgba(0,0,0,0.05); 
        }
        .queue-item.up-next { 
            border-left: 6px solid #10b981; 
            background-color: #ecfdf5; 
            border-color: #10b981;
        }
        .tech-name { font-size: 18px; font-weight: 500; }
        .up-next-badge { 
            color: #10b981; font-size: 14px; font-weight: bold; margin-left: 10px; 
        }
        
        /* Buttons */
        .controls button {
            border: none; padding: 10px 14px; border-radius: 6px; 
            cursor: pointer; font-weight: 600; margin-left: 5px;
            color: white;
        }
        .btn-mani { background-color: #ec4899; } /* Pink */
        .btn-pedi { background-color: #3b82f6; } /* Blue */
        .btn-skip { background-color: #9ca3af; } /* Gray */

        /* Summary Table */
        table { 
            width: 100%; border-collapse: collapse; background: white; 
            box-shadow: 0 1px 3px rgba(0,0,0,0.05); border-radius: 8px; 
            overflow: hidden; margin-bottom: 40px;
        }
        th, td { padding: 14px; text-align: left; border-bottom: 1px solid #e5e7eb; }
        th { background-color: #f3f4f6; font-weight: 600; }
    </style>
</head>
<body>

    <h1>Salon Rotation Tracker</h1>
    
    <div class="input-group">
        <input type="text" id="techName" placeholder="Enter technician name..." onkeypress="handleEnter(event)">
        <button onclick="addTech()">Add Tech</button>
    </div>

    <h2>Current Queue</h2>
    <div id="queueList"></div>

    <h2>Daily Summary</h2>
    <table>
        <thead>
            <tr>
                <th>Technician</th>
                <th>Manicures</th>
                <th>Pedicures</th>
                <th>Total Turns</th>
            </tr>
        </thead>
        <tbody id="summaryTable">
            <!-- Rows generate automatically -->
        </tbody>
    </table>

    <script>
        let techs = [];

        function render() {
            const queueList = document.getElementById('queueList');
            const summaryTable = document.getElementById('summaryTable');
            queueList.innerHTML = '';
            summaryTable.innerHTML = '';

            // Render Queue
            techs.forEach((tech, index) => {
                const isNext = index === 0;
                const div = document.createElement('div');
                div.className = `queue-item ${isNext ? 'up-next' : ''}`;
                
                let controls = '';
                if (isNext) {
                    controls = `
                        <div class="controls">
                            <button class="btn-mani" onclick="takeTurn(${index}, 'mani')">Mani</button>
                            <button class="btn-pedi" onclick="takeTurn(${index}, 'pedi')">Pedi</button>
                            <button class="btn-skip" onclick="takeTurn(${index}, 'skip')">Skip</button>
                        </div>
                    `;
                } else {
                    controls = `<div style="color: #6b7280; font-size: 14px;">Waiting...</div>`;
                }

                div.innerHTML = `
                    <div>
                        <span class="tech-name">${isNext ? '👉 ' : ''}${tech.name}</span>
                        ${isNext ? '<span class="up-next-badge">UP NEXT</span>' : ''}
                    </div>
                    ${controls}
                `;
                queueList.appendChild(div);
            });

            // Render Summary (Alphabetical Order)
            const sortedTechs = [...techs].sort((a, b) => a.name.localeCompare(b.name));
            sortedTechs.forEach(tech => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td><strong>${tech.name}</strong></td>
                    <td>${tech.mani}</td>
                    <td>${tech.pedi}</td>
                    <td><strong>${tech.mani + tech.pedi}</strong></td>
                `;
                summaryTable.appendChild(tr);
            });
        }

        function addTech() {
            const input = document.getElementById('techName');
            const name = input.value.trim();
            if (name) {
                techs.push({ name: name, mani: 0, pedi: 0 });
                input.value = '';
                render();
            }
        }

        function handleEnter(event) {
            if (event.key === 'Enter') addTech();
        }

        function takeTurn(index, type) {
            const tech = techs.splice(index, 1)[0]; 
            
            if (type === 'mani') tech.mani++;
            if (type === 'pedi') tech.pedi++;
            
            techs.push(tech); // Send to bottom
            render();
        }

        // Default staff members to get you started
        ['Anna', 'Bella', 'Chloe', 'David'].forEach(name => {
            techs.push({ name: name, mani: 0, pedi: 0 });
        });
        
        render();
    </script>
</body>
</html>


