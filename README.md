 # virtual management
 <!DOCTYPE html>
<html>
<head>
    <title>Virtual Memory Management</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>

<h1>Virtual Memory Management</h1>

<label>Page Reference String:</label>
<input type="text" id="pages" placeholder="1 2 3 4 1 2">

<label>Number of Frames:</label>
<input type="number" id="frames">

<div class="buttons">
    <button onclick="runAlgo('FIFO')">FIFO</button>
    <button onclick="runAlgo('LRU')">LRU</button>
    <button onclick="runAlgo('Optimal')">Optimal</button>
</div>

<pre id="output"></pre>

<script src="/static/script.js"></script>
</body>
</html>
from flask import Flask, request, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

def display_frames(frames):
    return " ".join(str(f) if f != -1 else "-" for f in frames)

def fifo(pages, f):
    frames = [-1] * f
    index = 0
    faults = 0
    result = "Page\tFrames\t\tStatus\n"
    result += "-" * 40 + "\n"

    for page in pages:
        if page not in frames:
            frames[index] = page
            index = (index + 1) % f
            faults += 1
            result += f"{page}\t{display_frames(frames)}\tFault\n"
        else:
            result += f"{page}\t{display_frames(frames)}\tHit\n"

    result += f"\nTotal Page Faults (FIFO) = {faults}"
    return result

@app.route('/run', methods=['POST'])
def run_algo():
    data = request.get_json()

    pages = list(map(int, data['pages'].split()))
    frames = int(data['frames'])
    algo = data['algorithm']

    if algo == "FIFO":
        output = fifo(pages, frames)
    else:
        output = "Algorithm not implemented"

    return jsonify(output)

@app.route('/')
def home():
    return "Backend is running successfully!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
    
