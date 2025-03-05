import { useState, useEffect } from "react";
import { v4 as uuidv4 } from "uuid";
import { getDatabase, ref, set, onValue, remove } from "firebase/database";
import { initializeApp } from "firebase/app";
import Draggable from "react-draggable";
import { Rnd } from "react-rnd";
import { FaImage, FaTimes, FaStickyNote, FaBars } from "react-icons/fa";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  databaseURL: "https://YOUR_FIREBASE_PROJECT_ID.firebaseio.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
const database = getDatabase(app);

const colors = ["bg-yellow-200", "bg-blue-200", "bg-green-200", "bg-red-200", "bg-purple-200"];

export default function StickyBoard() {
  const [notes, setNotes] = useState({});
  const [imageNotes, setImageNotes] = useState([]);
  const [sidebarOpen, setSidebarOpen] = useState(true);

  useEffect(() => {
    const notesRef = ref(database, "notes");
    const unsubscribe = onValue(notesRef, (snapshot) => {
      const data = snapshot.val();
      setNotes(data || {});
    });
    return () => unsubscribe();
  }, []);

  useEffect(() => {
    const message = "쓰레기는 쓰레기통에, 분리수거는 확실하게. 모범적인 시민 여러분이 됩시다.";
    const speech = new SpeechSynthesisUtterance(message);
    speech.lang = "ko-KR";
    speech.rate = 1;
    speech.pitch = 1.2;
    speechSynthesis.speak(speech);
  }, []);

  return (
    <div className="w-full h-screen flex">
      {/* Sidebar */}
      <div className={`transition-all duration-300 ${sidebarOpen ? "w-64" : "w-16"} bg-gray-200 p-4 h-full shadow-md flex flex-col`}>
        <button onClick={() => setSidebarOpen(!sidebarOpen)} className="mb-4 text-gray-700">
          <FaBars size={24} />
        </button>
        {sidebarOpen && (
          <>
            <button onClick={() => {}} className="mb-4 px-4 py-2 bg-blue-500 text-white rounded flex items-center">
              <FaStickyNote className="mr-2" /> 포스트잇
            </button>
            <label className="px-4 py-2 bg-green-500 text-white rounded flex items-center cursor-pointer">
              <FaImage className="mr-2" /> 이미지
              <input type="file" accept="image/*" className="hidden" />
            </label>
          </>
        )}
      </div>
      {/* Main Content */}
      <div className="relative w-full h-full border-4 border-gray-300 bg-white p-4 rounded-lg">
        {Object.values(notes).map((note) => (
          <Rnd
            key={note.id}
            default={{ x: note.x, y: note.y, width: note.width, height: note.height }}
            enableResizing={{ bottomRight: true, bottomLeft: true, topRight: true, topLeft: true }}
          >
            <div className={`relative p-2 rounded shadow-md ${note.color}`} style={{ width: "100%", height: "100%" }}>
              <button
                onClick={() => {}}
                className="absolute top-1 right-1 text-xs p-1 rounded bg-gray-500 text-white"
              >
                <FaTimes />
              </button>
              <textarea
                value={note.text}
                onChange={() => {}}
                className="w-full h-full bg-transparent border-none resize-none focus:outline-none"
              />
            </div>
          </Rnd>
        ))}
        {imageNotes.map((image) => (
          <Rnd
            key={image.id}
            default={{ x: image.x, y: image.y, width: image.width, height: image.height }}
            enableResizing={{ bottomRight: true, bottomLeft: true, topRight: true, topLeft: true }}
          >
            <div className="relative">
              <button
                onClick={() => {}}
                className="absolute top-1 right-1 text-xs p-1 rounded bg-gray-500 text-white"
              >
                <FaTimes />
              </button>
              <img src={image.imageUrl} alt="Uploaded" className="w-full h-full shadow-md" />
            </div>
          </Rnd>
        ))}
      </div>
    </div>
  );
}
