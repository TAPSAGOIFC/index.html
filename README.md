<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Tapsago IFC Dashboard</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      background: #f4f4f4;
    }
    #login {
      text-align: center;
      padding-top: 100px;
    }
    #dashboard {
      display: none;
    }
    .navbar {
      display: flex;
      background-color: #333;
      justify-content: center;
    }
    .navbar button {
      background-color: #333;
      color: white;
      padding: 14px 20px;
      border: none;
      cursor: pointer;
      font-size: 16px;
    }
    .navbar button:hover {
      background-color: #575757;
    }
    .content {
      padding: 20px;
    }
    .post {
      background: white;
      padding: 15px;
      margin-bottom: 15px;
      border-radius: 8px;
    }
    .post textarea {
      width: 100%;
      margin-top: 5px;
    }
    .comment {
      margin-left: 20px;
      margin-top: 5px;
      font-size: 14px;
    }
    .comment-box {
      margin-top: 10px;
      display: flex;
      gap: 10px;
    
}

    
    .post-controls {
      display: flex;
      gap: 10px;
      margin-top: 10px;
      align-items: center;
    }
    .like-button, .comment-toggle, .send-button {
      background-color: #007bff;
      color: white;
      border: none;
      padding: 5px 10px;
      border-radius: 5px;
      cursor: pointer;
    }
    .like-button:hover, .comment-toggle:hover, .send-button:hover {
      background-color: #0056b3;
    }
    .save-link {
      text-decoration: none;
      font-size: 14px;
      color: #555;
    }
 .hidden {
  display: none;
}

    
    


  </style>
  


</head>
<body>

<div id="login">
  <h2>Please Enter Password to Access Dashboard</h2>
  <input type="password" id="passwordInput" placeholder="Enter password">
  <br><br>
  <button onclick="checkPassword()">Login</button>
  <p id="errorMessage" style="color: red;"></p>
</div>

<div id="dashboard">
  <div class="navbar">
    <button onclick="showContent('updates')">Updates</button>
    <button onclick="showContent('fixtures')">Fixtures</button>
    <button onclick="showContent('team')">Team Players</button>
    <button onclick="showContent('table')">League Table</button>
    <button onclick="showSection('live')">Live Match</button>
<div id="onlineCount" style="color: white; padding: 8px; background: #28a745; text-align: center; font-weight: bold;">
  1 person online
</div>

  </div>
  <div class="content" id="mainContent">
    <h3>Welcome to Tapsagoi FC Dashboard</h3>
    <p>Select a section from the menu above.</p>
  </div>
</div>

<script>
  let posts = [];

  function checkPassword() {
  const password = document.getElementById("passwordInput").value;

  if (password === "tapsagoifc") {
    // Correct password: hide login, show dashboard, clear error
    document.getElementById("login").style.display = "none";
    document.getElementById("dashboard").style.display = "block";
    document.getElementById("errorMessage").innerText = "";  // Clear error message
   


  } else {
    // Wrong password: show error message
    document.getElementById("errorMessage").innerText = "Incorrect password!";
  }
}


  function showContent(section) {
  const content = document.getElementById("mainContent");

  if (section === "updates") {
    content.innerHTML = `
      <h3>Post an Update</h3>
      <textarea id="postText" placeholder="What is in your mind..."></textarea><br>
      <input type="file" id="postImage"><br><br>
      <button onclick="addPost()">Post</button>
      <h3>All Updates</h3>
      <div id="postsContainer"></div>
    `;
    renderPosts();
  } else if (section === "fixtures") {
    const days= [" ", " ", " ", " ", " ", " ", " "];
    content.innerHTML = `
      <h3>Fixtures</h3>
      <div style="overflow-x:auto;">
        <table border="1" cellpadding="10" cellspacing="0" style="width:100%; background-color: #fff; border-collapse: collapse;">
          <thead style="background-color: #0057a3; color: white;">
            <tr>
              <th>Day</th>
              <th>Match</th>
              <th>Venue</th>
              <th>Time</th>
            </tr>
          </thead>
          <tbody>
            ${Array.from({ length: 30 }, (_, i) => {
              const day = days[i % 7];
              return `
                <tr>
                  <td contenteditable="true">${day}</td>
                  <td contenteditable="true">   ${i + 1}</td>
                  <td contenteditable="true">Stadium ${i + 1}</td>
                  <td contenteditable="true">${(12 + i) % 24}:00</td>
                </tr>
              `;
            }).join("")}
          </tbody>
        </table>
      </div>
      <p style="font-size: 14px; color: #555;">Click any cell to edit.</p>
    `;
  } else if (section === "team") {
    content.innerHTML = `
      <h3>Team Players</h3>
      <div style="overflow-x:auto;">
        <table border="1" cellpadding="10" cellspacing="0" style="width:100%; background-color: #fff; border-collapse: collapse;">
          <thead style="background-color: #0057a3; color: white;">
            <tr>
              <th>#</th>
              <th>Name</th>
              <th>Position</th>
              <th>Jersey Number</th>
            </tr>
          </thead>
          <tbody>
            ${Array.from({ length: 25 }, (_, i) => `
              <tr>
                <td>${i + 1}</td>
                <td contenteditable="true">Player ${i + 1}</td>
                <td contenteditable="true">Position</td>
                <td contenteditable="true">${i + 1}</td>
              </tr>
            `).join("")}
          </tbody>
        </table>
      </div>
      <p style="font-size: 14px; color: #555;">Click any cell to edit.</p>
    `;
  } else if (section === "table") {
  content.innerHTML = `
    <h3>League Table</h3>
    
    
    <h4>Upload League Table Image</h4>
    <input type="file" id="leagueImageInput"><br><br>
    <button onclick="uploadLeagueImage()">Upload Image</button>
    <div id="leagueImagesContainer" style="margin-top: 20px;"></div>
  `;
}

}
function savePostsToStorage() {
  localStorage.setItem("tapsagoiPosts", JSON.stringify(posts));
}


  function addPost() {
  const text = document.getElementById("postText").value;
  const imageInput = document.getElementById("postImage");
  let image = "";

  if (imageInput.files && imageInput.files[0]) {
    const reader = new FileReader();
    reader.onload = function(e) {
      image = e.target.result;
      posts.unshift({ text, image, comments: [], likes: 0, showComments: false });
      savePostsToStorage(); // ✅ Save to localStorage
      renderPosts();        // ✅ Re-render
      document.getElementById("postText").value = "";
      imageInput.value = "";
    };
    reader.readAsDataURL(imageInput.files[0]);
  } else {
    posts.unshift({ text, image: "", comments: [], likes: 0, showComments: false });
    savePostsToStorage(); // ✅ Save to localStorage
    renderPosts();        // ✅ Re-render
    document.getElementById("postText").value = "";
  }
}

function deletePost(index) {
  if (confirm("Are you sure you want to delete this post?")) {
    posts.splice(index, 1); // Remove the post
    savePostsToStorage();   // Save changes
    renderPosts();          // Update the UI
  }
}

  function renderPosts() {
    const container = document.getElementById("postsContainer");
    container.innerHTML = "";
    posts.forEach((post, index) => {
      const postDiv = document.createElement("div");
      postDiv.className = "post";

      const saveLink = post.image ? `<a href="${post.image}" download="update_image.jpg" class="save-link">Download Image</a>` : "";

      postDiv.innerHTML = `
        <textarea onchange="editPost(${index}, this.value)">${post.text}</textarea>
      ${post.image ? `<div style="text-align:center;"><img src="${post.image}" style="width:40%; margin-top:10px; border-radius:8px;"></div>` : ""}


        <div class="post-controls">
          <button class="like-button" onclick="likePost(${index})">Like (${post.likes})</button>
          <button onclick="toggleComments(${index})">
  ${post.showComments ? 'Hide Comments' : 'View Comments'}
</button>

          ${saveLink}
           <button class="delete-button" onclick="deletePost(${index})">Delete</button> <!
        </div>
        <div class="comment-box">
          <input type="text" id="commentInput-${index}" placeholder="Add a comment...">
          <button class="send-button" onclick="addComment(${index})">Send</button>
        </div>
        <div id="comments-${index}" class="${post.showComments ? '' : 'hidden'}">
          ${post.comments.map(c => `<div class='comment'>- ${c}</div>`).join("")}
        </div>
      `;
      container.appendChild(postDiv);
    });
  }

  function editPost(index, newText) {
    posts[index].text = newText;
    savePostsToStorage();
  }

  function likePost(index) {
    posts[index].likes += 1;
    savePostsToStorage();
    renderPosts();
  }

  function addComment(index) {
    const input = document.getElementById(`commentInput-${index}`);
    const value = input.value.trim();
    if (value !== "") {
      posts[index].comments.push(value);
      input.value = "";
      posts[index].showComments = true;
      savePostsToStorage()
      renderPosts();
    }
  }
function deleteImage(index) {
  posts[index].image = "";
  savePostsToStorage();
  renderPosts();
}

  function toggleComments(index) {
  posts[index].showComments = !posts[index].showComments;
  renderPosts();
}

function showSection(section) {
  if (section === "live") {
    const content = document.getElementById("mainContent");
    content.innerHTML = `
      <h3>Live Match - Camera Recording</h3>
      <div style="position: relative; width: 100%; height: 450px; background: black; overflow: hidden; border-radius: 8px;">
        <!-- Fullscreen camera video -->
        <video id="preview" autoplay muted playsinline style="
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          object-fit: cover;
          z-index: 1;
          filter: brightness(0.7);
        "></video>

        <!-- Overlay container for editable match info -->
        <div style="
          position: absolute;
          top: 10px;
          left: 10px;
          right: 10px;
          color: white;
          font-family: Arial, sans-serif;
          font-size: 12px;
          z-index: 2;
          user-select: text;
        ">
          <!-- Team names on top -->
          <div contenteditable="true" style="
            font-weight: bold;
            margin-bottom: 4px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.6);
            padding-bottom: 2px;
            max-width: 100%;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
          ">
            Team A vs Team B
          </div>

          <!-- Scores and time in one line -->
          <div style="display: flex; gap: 10px; align-items: center;">
            <div contenteditable="true" style="
              background: rgba(0, 0, 0, 0.4);
              padding: 4px 8px;
              border-radius: 4px;
              min-width: 50px;
              text-align: center;
              ">
              Score A: 0
            </div>
            <div contenteditable="true" style="
              background: rgba(0, 0, 0, 0.4);
              padding: 4px 8px;
              border-radius: 4px;
              min-width: 50px;
              text-align: center;
              ">
              Score B: 0
            </div>
            <div contenteditable="true" style="
              background: rgba(0, 0, 0, 0.4);
              padding: 4px 8px;
              border-radius: 4px;
              min-width: 80px;
              text-align: center;
              ">
              Time: 00:00
            </div>
          </div>
        </div>
      </div>

      <div style="margin-top: 10px;">
        <button id="startBtn">Start Recording</button>
        <button id="stopBtn" disabled>Stop Recording</button>
      </div>

      <h4>Recorded Videos</h4>
      <div id="recordingsList"></div>
    `;

    initCameraRecording();
  }
}

   
  // ... your existing code above ...

  function initCameraRecording() {
    const video = document.getElementById("preview");
    const startBtn = document.getElementById("startBtn");
    const stopBtn = document.getElementById("stopBtn");
    const recordingsList = document.getElementById("recordingsList");

    recordedChunks = [];
    mediaRecorder = null;

    // Request camera access
    navigator.mediaDevices.getUserMedia({ video: true, audio: true })
      .then(stream => {
        video.srcObject = stream;

        mediaRecorder = new MediaRecorder(stream);

        mediaRecorder.ondataavailable = (event) => {
          if (event.data.size > 0) {
            recordedChunks.push(event.data);
          }
        };

        mediaRecorder.onstop = () => {
          const blob = new Blob(recordedChunks, { type: "video/webm" });
          recordedChunks = [];

          const url = URL.createObjectURL(blob);

          const videoElement = document.createElement("video");
          videoElement.controls = true;
          videoElement.src = url;
          videoElement.style.width = "100%";
          videoElement.style.maxWidth = "400px";
          videoElement.style.display = "block";
          videoElement.style.marginTop = "10px";

          const downloadLink = document.createElement("a");
          downloadLink.href = url;
          downloadLink.download = `recording_${new Date().toISOString()}.webm`;
          downloadLink.innerText = "Download Recording";
          downloadLink.style.display = "block";
          downloadLink.style.marginBottom = "20px";

          const container = document.createElement("div");
          container.appendChild(videoElement);
          container.appendChild(downloadLink);

          recordingsList.appendChild(container);
        };

        startBtn.disabled = false;
        stopBtn.disabled = true;

        startBtn.onclick = () => {
          if (mediaRecorder && mediaRecorder.state === "inactive") {
            mediaRecorder.start();
            startBtn.disabled = true;
            stopBtn.disabled = false;
          }
        };

        stopBtn.onclick = () => {
          if (mediaRecorder && mediaRecorder.state === "recording") {
            mediaRecorder.stop();
            startBtn.disabled = false;
            stopBtn.disabled = true;
          }
        };
      })
      .catch(err => {
        console.error("Error accessing camera: ", err);
        video.parentElement.innerHTML = "<p style='color:red;'>Unable to access camera.</p>";
      });
  }
 
// Generate a unique tab ID
const tabId = Date.now() + Math.random().toString(36).substr(2, 9);

// Interval to update this tab's heartbeat timestamp every 5 seconds
function updateHeartbeat() {
  localStorage.setItem('tapsagoiOnline_' + tabId, Date.now());
}

// Remove this tab's heartbeat from localStorage on unload
window.addEventListener('beforeunload', () => {
  localStorage.removeItem('tapsagoiOnline_' + tabId);
});

// Count how many tabs are "online" (heartbeat updated in last 10 seconds)
function countOnlineTabs() {
  const now = Date.now();
  const tenSecondsAgo = now - 10000;
  let count = 0;

  for (let i = 0; i < localStorage.length; i++) {
    const key = localStorage.key(i);
    if (key && key.startsWith('tapsagoiOnline_')) {
      const timestamp = parseInt(localStorage.getItem(key), 10);
      if (timestamp && timestamp > tenSecondsAgo) {
        count++;
      } else {
        // Remove stale entries
        localStorage.removeItem(key);
      }
    }
  }
  return count;
}

// Update the online count display
function updateOnlineCountDisplay() {
  const count = countOnlineTabs();
  const onlineText = count === 1 ? '1 person online' : `${count} people online`;
  const display = document.getElementById('onlineCount');
  if (display) display.textContent = onlineText;
}

// Listen to localStorage changes from other tabs
window.addEventListener('storage', (e) => {
  if (e.key && e.key.startsWith('tapsagoiOnline_')) {
    updateOnlineCountDisplay();
  }
});

// Start heartbeat and updating display
updateHeartbeat();
updateOnlineCountDisplay();
setInterval(() => {
  updateHeartbeat();
  updateOnlineCountDisplay();
}, 5000);

</script>
 
