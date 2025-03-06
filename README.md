<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bloodfuck Chaos</title>
    <style>
        body {
            font-family: 'Courier New', monospace;
            background: linear-gradient(45deg, #4a0000, #1a1a1a, #ff1a1a);
            color: #fff;
            text-align: center;
            padding: 10px;
            margin: 0;
            overflow-x: hidden;
            max-width: 100vw;
            box-sizing: border-box;
        }
        h1 {
            font-size: 1.8em;
            text-transform: uppercase;
            text-shadow: 2px 2px #000, 0 0 8px #ff1a1a;
            margin: 10px 0;
        }
        button {
            padding: 10px 20px;
            font-size: 1em;
            background: #4a0000;
            border: 2px solid #ff1a1a;
            color: #fff;
            border-radius: 5px;
            cursor: pointer;
            transition: transform 0.2s;
            margin: 5px;
            width: 90%;
            max-width: 300px;
        }
        button:hover {
            transform: scale(1.05);
            background: #ff1a1a;
        }
        .char-button {
            background: #1a1a1a;
            border: 1px solid #ff1a1a;
            width: 45%;
            margin: 2px;
            display: inline-block;
        }
        .char-button.selected {
            background: #ff1a1a;
            border: 2px solid #fff;
        }
        #battleground, #leaderboard, #records, #bracket, #customSelect {
            background: rgba(0, 0, 0, 0.85);
            padding: 10px;
            border-radius: 5px;
            margin: 10px auto;
            max-width: 100%;
            border: 1px solid #ff1a1a;
        }
        .popup {
            position: fixed;
            top: 10%;
            left: 2.5%;
            width: 95%;
            background: #1a1a1a;
            padding: 15px;
            border: 2px solid #ff1a1a;
            z-index: 1000;
            display: none;
            animation: pop 0.5s ease-in-out;
            max-height: 70vh;
            overflow-y: auto;
            box-sizing: border-box;
        }
        @keyframes pop {
            0% { transform: scale(0); }
            80% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        .close {
            position: absolute;
            top: 5px;
            right: 5px;
            color: #ff1a1a;
            cursor: pointer;
            font-size: 1.2em;
        }
        #battleLog {
            font-size: 1em;
            line-height: 1.3;
            text-align: left;
            padding: 5px;
            background: rgba(255, 26, 26, 0.15);
            border-radius: 5px;
        }
        .bracket-round {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
            margin: 5px 0;
        }
        .bracket-match {
            background: #4a0000;
            padding: 8px;
            border-radius: 5px;
            width: 45%;
            max-width: 120px;
            text-align: center;
            border: 1px solid #ff1a1a;
            margin: 2px;
            font-size: 0.9em;
        }
        .winner-celebration {
            font-size: 1.5em;
            color: #ff1a1a;
            text-shadow: 0 0 8px #fff, 0 0 15px #ff4500;
            animation: bleed 1s infinite;
        }
        @keyframes bleed {
            50% { opacity: 0.6; transform: scale(1.03); }
        }
        @media (max-width: 768px) {
            h1 { font-size: 1.5em; }
            button { font-size: 0.9em; padding: 8px 15px; }
            #battleLog { font-size: 0.9em; }
            .char-button { width: 45%; font-size: 0.8em; }
            .bracket-match { width: 45%; font-size: 0.8em; }
        }
    </style>
</head>
<body>
    <h1>Bloodfuck Chaos</h1>
    <div id="battleground">
        <button onclick="randomizeMatchup()">Next Gorefest</button>
        <button onclick="resetStats()">Reset the Slaughter</button>
        <div id="matchup"></div>
    </div>
    <div id="customSelect">
        <h2>Pick Your Victims</h2>
        <div id="charList"></div>
        <button onclick="customFight()" id="customFightBtn" disabled>Make ‘em Bleed!</button>
    </div>
    <div id="bracket">
        <h2>Sweet 8 Massacre</h2>
        <div id="quarterfinals"></div>
        <div id="semifinals"></div>
        <div id="finals"></div>
        <div id="champion"></div>
    </div>
    <div id="leaderboard">
        <h2>Kill Tally</h2>
        <div id="rankings"></div>
    </div>
    <div id="records">
        <h2>Blood Log</h2>
        <div id="matchHistory"></div>
    </div>
    <div id="battlePopup" class="popup">
        <span class="close" onclick="closePopup()">X</span>
        <div id="battleLog"></div>
    </div>

    <script>
        const characters = {
            "BANDO": { // Construction Worker
                color: "#ff4500",
                moves: [
                    "Jackhammer guts out (25)", "Wrecking ball head smash (20)", "Concrete face grind (15)", 
                    "Sledge to the dome (30)", "Brick jaw breaker (18)", "Nail gun eye shot (22)", 
                    "Bulldozer runs you flat (35)", "Crane drops on neck (19)", "Rebar thru chest (23)", "Hardhat caves skull (17)"
                ],
                wins: 0,
                losses: 0
            },
            "ATREM": { // Bad Mailman
                color: "#ff00ff",
                moves: [
                    "Tequila bottle neck slice (22)", "Mail truck guts you (28)", "Letter bomb face off (18)", 
                    "Package caves ribs (16)", "Rant fucks your brain (20)", "Mailbox smashes nose (25)", 
                    "Stamps in your eyes (19)", "Postage rips gut (17)", "Drunk tire skull pop (23)", "Envelope throat cut (21)"
                ],
                wins: 0,
                losses: 0
            },
            "SMITTY": { // Crazy Psychologist
                color: "#00ff00",
                moves: [
                    "Mind wipe bleeds brain (20)", "Couch choke out (24)", "Ink pen eye stab (15)", 
                    "Hypno makes you gut yourself (22)", "Scream pops ears (18)", "Brain fry snaps neck (28)", 
                    "Freudian throat punch (17)", "Chair back breaker (21)", "Rorschach rib snap (19)", "Psyche skull crack (25)"
                ],
                wins: 0,
                losses: 0
            },
            "DAR": { // Good Mailman
                color: "#00ffff",
                moves: [
                    "BMW slams spine (30)", "Minibike cuts legs off (17)", "Mailbox face fuck (16)", 
                    "Priority guts spill (22)", "Stampede stomps chest (15)", "Parcel head bash (26)", 
                    "Route ribs snap (20)", "Certified neck twist (18)", "Express elbow brain leak (24)", "Delivery rips torso (21)"
                ],
                wins: 0,
                losses: 0
            },
            "HADDIE": { // Rogue Student Counselor
                color: "#ffff00",
                moves: [
                    "Field goal kicks face in (25)", "Clipboard slits throat (19)", "Detention desk skull smash (16)", 
                    "Pep talk lungs collapse (22)", "Ruler stabs eye (18)", "Desk snaps spine (28)", 
                    "Hall pass chest stab (17)", "Guidance guts out (21)", "Suspension head pop (20)", "Counselor brain smash (24)"
                ],
                wins: 0,
                losses: 0
            },
            "CANNONZ": { // Athletic Coach
                color: "#ff1493",
                moves: [
                    "Basketball caves head (21)", "Fastball peels face (26)", "Whistle bursts ears (15)", 
                    "Dodgeball guts explode (23)", "Tackle snaps spine (19)", "Soccer kick rips head (29)", 
                    "Bat smashes skull (20)", "Huddle breaks ribs (18)", "Jump jack throat crush (24)", "Cleat guts spill (22)"
                ],
                wins: 0,
                losses: 0
            },
            "TREY": { // Nurse Manager
                color: "#1e90ff",
                moves: [
                    "IV floods veins out (19)", "Needle rips heart (25)", "Bandage chokes neck (16)", 
                    "Syringe stabs brain (23)", "Stethoscope snaps neck (18)", "Pills melt guts (28)", 
                    "Gurney cuts torso (21)", "Scalpel slices face (17)", "Defib fries skull (26)", "Needle pops artery (22)"
                ],
                wins: 0,
                losses: 0
            },
            "FROSTY": { // Beer Line Cleaner
                color: "#adff2f",
                moves: [
                    "Keg smashes skull (26)", "Beer hose drowns lungs (19)", "Van runs spine flat (24)", 
                    "Hose blasts eyes out (17)", "Tap cracks head (21)", "Foam rips throat (29)", 
                    "Cap slashes artery (18)", "Brew breaks ribs (23)", "Line pierces gut (20)", "Pint glass face smash (25)"
                ],
                wins: 0,
                losses: 0
            }
        };

        let currentMatch = [];
        let matchHistory = [];
        let bracket = {
            quarterfinals: [],
            semifinals: [],
            finals: [],
            champion: null
        };
        let selectedChars = [];

        function randomMove(moves) {
            return moves[Math.floor(Math.random() * moves.length)];
        }

        function randomizeMatchup() {
            const keys = Object.keys(characters);
            if (bracket.quarterfinals.length < 4) {
                let char1, char2;
                do {
                    char1 = keys[Math.floor(Math.random() * keys.length)];
                    char2 = keys[Math.floor(Math.random() * keys.length)];
                } while (char1 === char2 || bracket.quarterfinals.some(m => m.includes(char1) || m.includes(char2)));
                currentMatch = [char1, char2];
                bracket.quarterfinals.push(currentMatch);
            } else if (bracket.semifinals.length < 2 && bracket.quarterfinals.every(m => m.winner)) {
                currentMatch = [bracket.quarterfinals[bracket.semifinals.length * 2].winner, bracket.quarterfinals[bracket.semifinals.length * 2 + 1].winner];
                bracket.semifinals.push(currentMatch);
            } else if (bracket.finals.length === 0 && bracket.semifinals.every(m => m.winner)) {
                currentMatch = [bracket.semifinals[0].winner, bracket.semifinals[1].winner];
                bracket.finals.push(currentMatch);
            } else {
                return;
            }
            document.getElementById("matchup").innerHTML = `
                <h2>${currentMatch[0]} vs ${currentMatch[1]}</h2>
                <button onclick="beginFight()">Rip ‘em Apart!</button>
            `;
            updateBracket();
        }

        function selectChar(char) {
            const index = selectedChars.indexOf(char);
            if (index === -1 && selectedChars.length < 2) {
                selectedChars.push(char);
                document.getElementById(`char-${char}`).classList.add("selected");
            } else if (index !== -1) {
                selectedChars.splice(index, 1);
                document.getElementById(`char-${char}`).classList.remove("selected");
            }
            document.getElementById("customFightBtn").disabled = selectedChars.length !== 2;
        }

        function customFight() {
            if (selectedChars.length !== 2) return;
            currentMatch = [...selectedChars];
            document.getElementById("matchup").innerHTML = `
                <h2>${currentMatch[0]} vs ${currentMatch[1]}</h2>
                <button onclick="beginFight()">Rip ‘em Apart!</button>
            `;
            selectedChars.forEach(char => document.getElementById(`char-${char}`).classList.remove("selected"));
            selectedChars = [];
            document.getElementById("customFightBtn").disabled = true;
        }

        async function beginFight() {
            if (currentMatch.length !== 2) return;
            const char1 = characters[currentMatch[0]];
            const char2 = characters[currentMatch[1]];
            const popup = document.getElementById("battlePopup");
            const log = document.getElementById("battleLog");
            log.innerHTML = "";
            popup.style.display = "block";

            let score1 = 0, score2 = 0;
            const maxRounds = Math.floor(Math.random() * 3) + 2;
            let round = 0;
            let fightOver = false;

            await addLog(log, "☠️ Gore drops now!", "#fff");
            await new Promise(r => setTimeout(r, 2000));

            while (round < maxRounds && !fightOver) {
                const move1 = randomMove(char1.moves);
                const move2 = randomMove(char2.moves);
                const points1 = parseInt(move1.match(/\d+/)[0]);
                const points2 = parseInt(move2.match(/\d+/)[0]);
                score1 += points1;
                score2 += points2;

                await addLog(log, `${currentMatch[0]} fucks ${currentMatch[1]} with ${move1}! (${points1})`, char1.color);
                await new Promise(r => setTimeout(r, 2000));
                if (Math.random() < 0.25 && points1 > 20) {
                    await addLog(log, `${currentMatch[1]}’s guts splash—fucked!`, char1.color);
                    fightOver = true;
                    score1 += 50;
                    break;
                }

                await addLog(log, `${currentMatch[1]} hits back with ${move2}! (${points2})`, char2.color);
                await new Promise(r => setTimeout(r, 2000));
                if (Math.random() < 0.25 && points2 > 20) {
                    await addLog(log, `${currentMatch[0]}’s head’s gone—done!`, char2.color);
                    fightOver = true;
                    score2 += 50;
                    break;
                }

                round++;
            }

            if (!fightOver) {
                await addLog(log, `${currentMatch[0]}: ${score1}`, char1.color);
                await addLog(log, `${currentMatch[1]}: ${score2}`, char2.color);
                await new Promise(r => setTimeout(r, 2000));
            }

            const winner = score1 > score2 ? currentMatch[0] : currentMatch[1];
            const loser = score1 > score2 ? currentMatch[1] : currentMatch[0];
            characters[winner].wins++;
            characters[loser].losses++;
            currentMatch.winner = winner;

            await addLog(log, `<span class="winner-celebration">☠️ ${winner} owns ${loser}’s dead ass!</span>`, characters[winner].color);
            matchHistory.push(`${currentMatch[0]} vs ${currentMatch[1]} - ${winner} kills (${score1}-${score2})`);
            updateRecords();
            updateLeaderboard();

            if (bracket.finals.length === 1 && bracket.finals[0].winner) {
                bracket.champion = bracket.finals[0].winner;
                await addLog(log, `<span class="winner-celebration">👑 ${bracket.champion} runs this shit!</span>`, characters[bracket.champion].color);
            }
            updateBracket();
        }

        async function addLog(log, text, color) {
            const div = document.createElement("div");
            div.innerHTML = text;
            div.style.color = color;
            log.appendChild(div);
            log.scrollTop = log.scrollHeight;
        }

        function closePopup() {
            document.getElementById("battlePopup").style.display = "none";
            document.getElementById("matchup").innerHTML = "";
            currentMatch = [];
        }

        function updateLeaderboard() {
            const rankings = Object.entries(characters)
                .sort((a, b) => (b[1].wins - b[1].losses) - (a[1].wins - a[1].losses))
                .map(([key, char]) => `${key}: ${char.wins}K-${char.losses}D`)
                .join("<br>");
            document.getElementById("rankings").innerHTML = rankings;
        }

        function updateRecords() {
            document.getElementById("matchHistory").innerHTML = matchHistory.join("<br>");
        }

        function updateBracket() {
            const qf = document.getElementById("quarterfinals");
            const sf = document.getElementById("semifinals");
            const fn = document.getElementById("finals");
            const ch = document.getElementById("champion");
            qf.innerHTML = "<h3>Quarters</h3><div class='bracket-round'>" + 
                bracket.quarterfinals.map(m => `<div class='bracket-match'>${m[0]} vs ${m[1]}<br>${m.winner ? m.winner : "TBD"}</div>`).join("") + "</div>";
            sf.innerHTML = "<h3>Semis</h3><div class='bracket-round'>" + 
                bracket.semifinals.map(m => `<div class='bracket-match'>${m[0]} vs ${m[1]}<br>${m.winner ? m.winner : "TBD"}</div>`).join("") + "</div>";
            fn.innerHTML = "<h3>Final</h3><div class='bracket-round'>" + 
                bracket.finals.map(m => `<div class='bracket-match'>${m[0]} vs ${m[1]}<br>${m.winner ? m.winner : "TBD"}</div>`).join("") + "</div>";
            ch.innerHTML = bracket.champion ? `<h3>Gore Lord</h3><div class='bracket-match winner-celebration'>${bracket.champion}</div>` : "";
        }

        function resetStats() {
            Object.keys(characters).forEach(key => {
                characters[key].wins = 0;
                characters[key].losses = 0;
            });
            matchHistory = [];
            bracket = { quarterfinals: [], semifinals: [], finals: [], champion: null };
            document.getElementById("matchup").innerHTML = "";
            currentMatch = [];
            selectedChars = [];
            document.querySelectorAll(".char-button").forEach(btn => btn.classList.remove("selected"));
            document.getElementById("customFightBtn").disabled = true;
            updateLeaderboard();
            updateRecords();
            updateBracket();
        }

        function initCharList() {
            const charList = document.getElementById("charList");
            charList.innerHTML = Object.keys(characters).map(char => 
                `<button class="char-button" id="char-${char}" onclick="selectChar('${char}')">${char}</button>`
            ).join("");
        }

        // Initialize
        initCharList();
        updateLeaderboard();
        updateRecords();
        updateBracket();
    </script>
</body>
</html>
