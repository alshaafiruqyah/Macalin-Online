

 # Macalin-Online
We Teach English, Arabic, Islamic Books Online
import React, { useState } from 'react';

// Profanity & Vulgarity Filter Engine
const FORBIDDEN_WORDS = ['flirt', 'vulgar', 'badword', 'abuse', 'foul'];

export default function MacalinApp() {
  // -------------------------------------------------------------
  // STATE MANAGEMENT
  // -------------------------------------------------------------
  const [currentUser, setCurrentUser] = useState(null); // { id, name, emailPhone, role, isBanned }
  const [authMode, setAuthMode] = useState('login'); // 'login' | 'signup'
  
  // Auth Form Inputs
  const [inputEmailPhone, setInputEmailPhone] = useState('');
  const [inputPassword, setInputPassword] = useState('');
  const [inputName, setInputName] = useState('');

  // Classroom & Navigation States
  const [activeTab, setActiveTab] = useState('chat'); // 'chat' | 'call' | 'uploads'
  const [callType, setCallType] = useState(null); // null | 'voice' | 'video'
  const [isMicMuted, setIsMicMuted] = useState(false);
  const [isVideoOff, setIsVideoOff] = useState(false);

  // Chat & Moderation States
  const [messages, setMessages] = useState([
    { id: 1, sender: 'Teacher Ali', text: 'Assalamu Alaikum! Welcome to today’s English & Islamic Studies lesson.', role: 'admin', time: '10:00 AM' }
  ]);
  const [chatInput, setChatInput] = useState('');
  const [warningMessage, setWarningMessage] = useState('');

  // File Upload State
  const [uploadedFiles, setUploadedFiles] = useState([
    { id: 1, title: 'English Grammar Basics - Chapter 1', type: 'video', size: '45 MB' },
    { id: 2, title: 'Surah Al-Fatiha Recitation & Meaning', type: 'audio', size: '12 MB' }
  ]);
  const [uploadTitle, setUploadTitle] = useState('');

  // User Roster & Ban Management (Admin Control)
  const [userList, setUserList] = useState([
    { id: 'u1', name: 'Amina Abdi', emailPhone: '0615551122', isBanned: false },
    { id: 'u2', name: 'Hassan Mohamed', emailPhone: 'hassan@example.com', isBanned: false },
    { id: 'u3', name: 'Farhia Jama', emailPhone: '0619998877', isBanned: true }
  ]);

  // -------------------------------------------------------------
  // HANDLERS
  // -------------------------------------------------------------
  
  // Login / Signup Handler
  const handleAuth = (e) => {
    e.preventDefault();
    
    // Check if the user is Teacher Ali (Admin)
    const isAdmin = 
      inputEmailPhone.trim() === 'aliyousuf2023@gmail.com' || 
      inputEmailPhone.trim() === '0611611735' ||
      inputEmailPhone.trim() === '0621611735';

    // Check if user is on the banned list
    const existingBannedUser = userList.find(
      u => u.emailPhone.toLowerCase() === inputEmailPhone.toLowerCase() && u.isBanned
    );

    if (existingBannedUser) {
      alert('⛔ Access Denied: This account/number has been permanently banned for violating community rules.');
      return;
    }

    setCurrentUser({
      id: Date.now().toString(),
      name: inputName.trim() || (isAdmin ? 'Teacher Ali (Admin)' : 'Student'),
      emailPhone: inputEmailPhone.trim(),
      role: isAdmin ? 'admin' : 'student',
      isBanned: false
    });
  };

  // Chat Submission with Moderation Engine
  const handleSendMessage = (e) => {
    e.preventDefault();
    if (!chatInput.trim()) return;

    // Check against forbidden vulgarity keywords
    const isViolation = FORBIDDEN_WORDS.some(word => 
      chatInput.toLowerCase().includes(word)
    );

    if (isViolation) {
      setWarningMessage('⚠️ Warning: Vulgar language, flirting, or foul words are strictly prohibited! Only lessons and educational talk allowed.');
      return;
    }

    setWarningMessage('');
    const newMessage = {
      id: Date.now(),
      sender: currentUser.name,
      text: chatInput,
      role: currentUser.role,
      time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
    };

    setMessages((prev) => [...prev, newMessage]);
    setChatInput('');
  };

  // File Upload Handler
  const handleUploadFile = (e) => {
    e.preventDefault();
    if (!uploadTitle.trim()) return;

    setUploadedFiles([
      ...uploadedFiles,
      { id: Date.now(), title: uploadTitle, type: 'video', size: '25 MB' }
    ]);
    setUploadTitle('');
    alert('Lesson successfully uploaded!');
  };

  // Toggle Ban Status (Admin Only)
  const handleToggleBan = (userId) => {
    setUserList(userList.map(u => u.id === userId ? { ...u, isBanned: !u.isBanned } : u));
  };

  // -------------------------------------------------------------
  // VIEW 1: AUTHENTICATION SCREEN (LOG IN / SIGN UP)
  // -------------------------------------------------------------
  if (!currentUser) {
    return (
      <div className="min-h-screen bg-slate-100 flex items-center justify-center p-4">
        <div className="bg-white p-8 rounded-2xl shadow-xl w-full max-w-md border border-emerald-100">
          
          {/* Logo & Header */}
          <div className="text-center mb-6">
            <div className="w-16 h-16 bg-emerald-700 text-white font-black text-2xl flex items-center justify-center rounded-2xl mx-auto shadow-md mb-2">
              M
            </div>
            <h1 className="text-3xl font-extrabold text-emerald-900">Macalin 📚</h1>
            <p className="text-xs text-gray-500 mt-1">English & Islamic Books Platform</p>
          </div>

          {/* Auth Mode Switcher */}
          <div className="flex mb-6 border-b">
            <button 
              className={`flex-1 py-2 font-semibold text-sm ${authMode === 'login' ? 'border-b-2 border-emerald-600 text-emerald-700' : 'text-gray-400'}`}
              onClick={() => setAuthMode('login')}
            >
              Log In
            </button>
            <button 
              className={`flex-1 py-2 font-semibold text-sm ${authMode === 'signup' ? 'border-b-2 border-emerald-600 text-emerald-700' : 'text-gray-400'}`}
              onClick={() => setAuthMode('signup')}
            >
              Sign Up
            </button>
          </div>

          {/* Form */}
          <form onSubmit={handleAuth} className="space-y-4">
            {authMode === 'signup' && (
              <div>
                <label className="block text-xs font-semibold text-gray-600 mb-1">Full Name</label>
                <input 
                  type="text" 
                  required 
                  placeholder="Enter your name" 
                  className="w-full p-3 border rounded-lg text-sm focus:outline-emerald-600"
                  value={inputName}
                  onChange={(e) => setInputName(e.target.value)}
                />
              </div>
            )}

            <div>
              <label className="block text-xs font-semibold text-gray-600 mb-1">Phone Number or Email</label>
              <input 
                type="text" 
                required 
                placeholder="0611611735 or aliyousuf2023@gmail.com" 
                className="w-full p-3 border rounded-lg text-sm focus:outline-emerald-600"
                value={inputEmailPhone}
                onChange={(e) => setInputEmailPhone(e.target.value)}
              />
            </div>

            <div>
              <label className="block text-xs font-semibold text-gray-600 mb-1">Password</label>
              <input 
                type="password" 
                required 
                placeholder="••••••••" 
                className="w-full p-3 border rounded-lg text-sm focus:outline-emerald-600"
                value={inputPassword}
                onChange={(e) => setInputPassword(e.target.value)}
              />
            </div>

            <button type="submit" className="w-full bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-3 rounded-lg transition text-sm">
              {authMode === 'login' ? 'Log In to Classroom' : 'Create Student Account'}
            </button>
          </form>

          {/* Support Contacts */}
          <div className="mt-6 pt-4 border-t text-center text-xs text-gray-500">
            <p className="font-semibold text-gray-700">Need help logging in?</p>
            <p>📧 aliyousuf2023@gmail.com</p>
            <p>📱 0611611735 / 0621611735</p>
          </div>
        </div>
      </div>
    );
  }

  // -------------------------------------------------------------
  // VIEW 2: MAIN CLASSROOM DASHBOARD
  // -------------------------------------------------------------
  return (
    <div className="min-h-screen bg-gray-50 flex flex-col font-sans">
      
      {/* App Header */}
      <header className="bg-emerald-800 text-white p-4 shadow-md flex justify-between items-center">
        <div className="flex items-center gap-3">
          <div className="w-10 h-10 bg-emerald-600 text-white font-black text-xl flex items-center justify-center rounded-xl">
            M
          </div>
          <div>
            <h1 className="text-xl font-bold leading-none">Macalin 📚</h1>
            <p className="text-xs text-emerald-200 mt-1">Teaching English & Islamic Books</p>
          </div>
        </div>

        <div className="flex items-center gap-4">
          <div className="text-right hidden sm:block">
            <p className="text-xs font-bold">{currentUser.name}</p>
            <span className={`text-[10px] px-2 py-0.5 rounded-full ${currentUser.role === 'admin' ? 'bg-amber-400 text-amber-950 font-bold' : 'bg-emerald-700 text-emerald-100'}`}>
              {currentUser.role === 'admin' ? 'Teacher (Admin)' : 'Student'}
            </span>
          </div>
          <button 
            onClick={() => setCurrentUser(null)} 
            className="bg-emerald-900 hover:bg-emerald-950 px-3 py-1.5 rounded-lg text-xs font-semibold"
          >
            Logout
          </button>
        </div>
      </header>

      {/* Strict Guidelines Banner */}
      <div className="bg-amber-50 border-b border-amber-200 text-amber-900 text-xs px-4 py-2 text-center font-medium">
        ⚠️ <strong>Classroom Rules:</strong> Only English & Islamic educational discussions allowed. Vulgarity, flirting, or inappropriate behavior will result in an immediate permanent ban.
      </div>

      {/* Main Workspace */}
      <div className="flex-1 flex flex-col lg:flex-row max-w-7xl w-full mx-auto p-4 gap-4">
        
        {/* Main Content Area */}
        <div className="flex-1 bg-white rounded-xl shadow-sm border flex flex-col min-h-[550px]">
          
          {/* Top Navigation Tabs */}
          <div className="p-3 border-b flex gap-2 bg-gray-50 rounded-t-xl overflow-x-auto">
            <button 
              onClick={() => setActiveTab('chat')}
              className={`px-4 py-2 rounded-lg text-xs font-bold flex items-center gap-2 ${activeTab === 'chat' ? 'bg-emerald-700 text-white' : 'bg-white text-gray-600 border'}`}
            >
              💬 Classroom Chat
            </button>
            <button 
              onClick={() => setActiveTab('call')}
              className={`px-4 py-2 rounded-lg text-xs font-bold flex items-center gap-2 ${activeTab === 'call' ? 'bg-emerald-700 text-white' : 'bg-white text-gray-600 border'}`}
            >
              📹 Voice & Video Call Studio
            </button>
            <button 
              onClick={() => setActiveTab('uploads')}
              className={`px-4 py-2 rounded-lg text-xs font-bold flex items-center gap-2 ${activeTab === 'uploads' ? 'bg-emerald-700 text-white' : 'bg-white text-gray-600 border'}`}
            >
              📁 Video & Audio Lessons
            </button>
          </div>

          {/* TAB 1: MODERATED CHAT */}
          {activeTab === 'chat' && (
            <div className="flex-1 flex flex-col justify-between p-4">
              <div className="space-y-3 overflow-y-auto max-h-[420px] p-2">
                {messages.map((m) => (
                  <div 
                    key={m.id} 
                    className={`p-3 rounded-xl max-w-lg ${m.role === 'admin' ? 'bg-emerald-50 border border-emerald-200 ml-auto' : 'bg-gray-100'}`}
                  >
                    <div className="flex justify-between items-center mb-1 gap-4">
                      <span className="text-xs font-bold text-emerald-900">{m.sender}</span>
                      <span className="text-[10px] text-gray-400">{m.time}</span>
                    </div>
                    <p className="text-sm text-gray-800">{m.text}</p>
                  </div>
                ))}
              </div>

              {/* Violation Warning Box */}
              {warningMessage && (
                <div className="bg-red-50 border border-red-200 text-red-700 text-xs p-3 rounded-lg mb-2 font-semibold">
                  {warningMessage}
                </div>
              )}

              {/* Message Input Form */}
              <form onSubmit={handleSendMessage} className="flex gap-2 border-t pt-3">
                <input 
                  type="text" 
                  placeholder="Ask a question about the lesson..." 
                  className="flex-1 p-2.5 border rounded-lg text-sm focus:outline-emerald-600"
                  value={chatInput}
                  onChange={(e) => setChatInput(e.target.value)}
                />
                <button type="submit" className="bg-emerald-700 hover:bg-emerald-800 text-white px-5 py-2.5 rounded-lg text-sm font-bold">
                  Send
                </button>
              </form>
            </div>
          )}

          {/* TAB 2: VOICE & VIDEO CALL STUDIO */}
          {activeTab === 'call' && (
            <div className="flex-1 p-6 flex flex-col items-center justify-center bg-slate-900 text-white rounded-b-xl min-h-[400px]">
              {!callType ? (
                <div className="text-center space-y-4 max-w-md">
                  <h3 className="text-xl font-bold">Live Audio & Video Classroom</h3>
                  <p className="text-xs text-slate-400">Join real-time interactive lectures hosted by Teacher Ali.</p>
                  <div className="flex justify-center gap-4 pt-2">
                    <button 
                      onClick={() => setCallType('voice')}
                      className="bg-emerald-600 hover:bg-emerald-700 px-5 py-3 rounded-xl font-bold text-xs flex items-center gap-2"
                    >
                      🎙️ Start Voice Call
                    </button>
                    <button 
                      onClick={() => setCallType('video')}
                      className="bg-blue-600 hover:bg-blue-700 px-5 py-3 rounded-xl font-bold text-xs flex items-center gap-2"
                    >
                      📹 Start Video Call
                    </button>
                  </div>
                </div>
              ) : (
                <div className="w-full max-w-2xl text-center space-y-4">
                  {/* Video/Audio Stage Box */}
                  <div className="bg-slate-800 h-64 rounded-2xl flex flex-col items-center justify-center border border-slate-700 relative overflow-hidden">
                    {isVideoOff && callType === 'video' ? (
                      <div className="text-slate-400 text-xs">Camera Turned Off</div>
                    ) : (
                      <div className="text-center space-y-2">
                        <div className="w-20 h-20 bg-emerald-700 rounded-full flex items-center justify-center mx-auto text-2xl font-black">
                          {currentUser.name[0]}
                        </div>
                        <p className="text-sm font-bold">{currentUser.name}</p>
                        <span className="text-[10px] bg-emerald-900 text-emerald-200 px-2 py-0.5 rounded-full">
                          {callType === 'video' ? '📹 Video Stream Active' : '🎙️ Voice Stream Active'}
                        </span>
                      </div>
                    )}
                  </div>

                  {/* Call Controls */}
                  <div className="flex justify-center items-center gap-3">
                    <button 
                      onClick={() => setIsMicMuted(!isMicMuted)}
                      className={`px-4 py-2 rounded-lg text-xs font-bold ${isMicMuted ? 'bg-amber-600' : 'bg-slate-700'}`}
                    >
                      {isMicMuted ? 'Unmute Mic' : 'Mute Mic'}
                    </button>

                    {callType === 'video' && (
                      <button 
                        onClick={() => setIsVideoOff(!isVideoOff)}
                        className={`px-4 py-2 rounded-lg text-xs font-bold ${isVideoOff ? 'bg-amber-600' : 'bg-slate-700'}`}
                      >
                        {isVideoOff ? 'Turn Camera On' : 'Turn Camera Off'}
                      </button>
                    )}

                    <button 
                      onClick={() => setCallType(null)}
                      className="bg-red-600 hover:bg-red-700 text-white px-5 py-2 rounded-lg font-bold text-xs"
                    >
                      End Call
                    </button>
                  </div>
                </div>
              )}
            </div>
          )}

          {/* TAB 3: LESSON UPLOADS */}
          {activeTab === 'uploads' && (
            <div className="p-6 flex-1 flex flex-col">
              <h3 className="text-lg font-bold text-gray-800 mb-2">Lesson Library</h3>
              
              {/* Upload Box (Visible to Admin or Students) */}
              <form onSubmit={handleUploadFile} className="border-2 border-dashed border-emerald-300 bg-emerald-50/50 p-6 rounded-xl mb-6 text-center">
                <p className="text-xs font-bold text-emerald-900 mb-2">Upload New Video or Audio Lesson</p>
                <input 
                  type="text" 
                  required 
                  placeholder="Lesson Title (e.g., Islamic Studies - Chapter 3)"
                  className="w-full max-w-md p-2 border rounded-lg text-xs mb-3 focus:outline-emerald-600"
                  value={uploadTitle}
                  onChange={(e) => setUploadTitle(e.target.value)}
                />
                <br />
                <button type="submit" className="bg-emerald-700 hover:bg-emerald-800 text-white px-6 py-2 rounded-lg text-xs font-bold">
                  Upload Media File
                </button>
              </form>

              {/* Uploaded File Roster */}
              <div className="space-y-3">
                <h4 className="text-xs font-bold text-gray-500 uppercase">Available Lessons</h4>
                {uploadedFiles.map((file) => (
                  <div key={file.id} className="flex items-center justify-between p-3 border rounded-xl bg-gray-50">
                    <div className="flex items-center gap-3">
                      <span className="text-xl">{file.type === 'video' ? '🎬' : '🎧'}</span>
                      <div>
                        <p className="text-xs font-bold text-gray-800">{file.title}</p>
                        <p className="text-[10px] text-gray-400">{file.type.toUpperCase()} • {file.size}</p>
                      </div>
                    </div>
                    <button className="bg-emerald-100 text-emerald-800 hover:bg-emerald-200 px-3 py-1.5 rounded-lg text-xs font-bold">
                      Play Lesson
                    </button>
                  </div>
                ))}
              </div>
            </div>
          )}

        </div>

        {/* Sidebar: Teacher Ban Control Panel & Contacts */}
        <div className="w-full lg:w-80 space-y-4">
          
          {/* Admin Moderation Panel */}
          {currentUser.role === 'admin' ? (
            <div className="bg-white p-4 rounded-xl shadow-sm border">
              <h3 className="text-sm font-bold text-gray-800 mb-1">🛡️ Teacher Ban Control Panel</h3>
              <p className="text-[11px] text-gray-500 mb-4">Click "Ban" to block users violating rules.</p>

              <div className="space-y-2">
                {userList.map((user) => (
                  <div key={user.id} className="flex items-center justify-between p-2.5 border rounded-lg bg-gray-50">
                    <div>
                      <p className="text-xs font-bold text-gray-800">{user.name}</p>
                      <p className="text-[10px] text-gray-400">{user.emailPhone}</p>
                    </div>
                    <button 
                      onClick={() => handleToggleBan(user.id)}
                      className={`px-3 py-1 rounded text-[11px] font-bold ${user.isBanned ? 'bg-emerald-100 text-emerald-800' : 'bg-red-100 text-red-700 hover:bg-red-200'}`}
                    >
                      {user.isBanned ? 'Unban' : 'Ban'}
                    </button>
                  </div>
                ))}
              </div>
            </div>
          ) : (
            <div className="bg-emerald-50 p-4 rounded-xl border border-emerald-200">
              <h3 className="text-xs font-bold text-emerald-900 mb-1">📖 Welcome Student</h3>
              <p className="text-xs text-emerald-800">
                Ensure you stay respectful in chats and live calls. All lessons are monitored.
              </p>
            </div>
          )}

          {/* Contact Card */}
          <div className="bg-white p-4 rounded-xl shadow-sm border space-y-2 text-xs">
            <h4 className="font-bold text-gray-800 border-b pb-2">📞 Macalin Support</h4>
            <p className="text-gray-600"><strong>Email:</strong> aliyousuf2023@gmail.com</p>
            <p className="text-gray-600"><strong>Primary Phone:</strong> 0611611735</p>
            <p className="text-gray-600"><strong>Secondary Phone:</strong> 0621611735</p>
          </div>

        </div>

      </div>
    </div>
  );
}
npx create-react-app macalin-app
cd macalin-app
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
}
@tailwind base;
@tailwind components;
@tailwind utilities;
<a href="https://app.netlify.com/start/deploy?repository=YOUR_GITHUB_REPO_URL">
  <img src="https://www.netlify.com/img/deploy/button.svg" alt="Deploy to Netlify">
</a>

