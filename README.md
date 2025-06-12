# Miss.-Findmylove
A dating site
// React App: Ms. FindMyLove - MVP Version with Admin Panel

import React, { useState, useEffect } from 'react'; import './App.css'; import { initializeApp } from 'firebase/app'; import { getFirestore, collection, addDoc, getDocs } from 'firebase/firestore';

// Firebase configuration const firebaseConfig = { apiKey: 'YOUR_FIREBASE_API_KEY', authDomain: 'YOUR_FIREBASE_AUTH_DOMAIN', projectId: 'YOUR_FIREBASE_PROJECT_ID', storageBucket: 'YOUR_FIREBASE_STORAGE_BUCKET', messagingSenderId: 'YOUR_FIREBASE_MESSAGING_SENDER_ID', appId: 'YOUR_FIREBASE_APP_ID' };

// Initialize Firebase const app = initializeApp(firebaseConfig); const db = getFirestore(app);

const App = () => { const [formData, setFormData] = useState({ name: '', age: '', gender: '', preference: '', email: '' });

const [submitted, setSubmitted] = useState(false); const [adminMode, setAdminMode] = useState(false); const [submissions, setSubmissions] = useState([]);

const handleChange = (e) => { setFormData({ ...formData, [e.target.name]: e.target.value }); };

const handleSubmit = async (e) => { e.preventDefault(); try { await addDoc(collection(db, 'submissions'), formData); setSubmitted(true); } catch (err) { console.error('Submission error:', err); alert('Something went wrong. Try again.'); } };

const loadSubmissions = async () => { try { const querySnapshot = await getDocs(collection(db, 'submissions')); const data = querySnapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() })); setSubmissions(data); } catch (err) { console.error('Error fetching submissions:', err); } };

useEffect(() => { if (adminMode) { loadSubmissions(); } }, [adminMode]);

return ( <div className="min-h-screen bg-pink-50 p-6"> <h1 className="text-4xl font-bold text-pink-600 text-center mb-6">Ms. FindMyLove ❤️</h1>

<div className="text-center mb-4">
    <button
      onClick={() => setAdminMode(!adminMode)}
      className="text-sm underline text-blue-500"
    >
      {adminMode ? 'Exit Admin Mode' : 'Admin Login'}
    </button>
  </div>

  {adminMode ? (
    <div className="max-w-4xl mx-auto bg-white p-6 rounded-xl shadow-md">
      <h2 className="text-2xl font-semibold mb-4">Submissions</h2>
      {submissions.length === 0 ? (
        <p>No data yet.</p>
      ) : (
        <ul className="space-y-4">
          {submissions.map((entry) => (
            <li key={entry.id} className="border-b pb-2">
              <p><strong>Name:</strong> {entry.name}</p>
              <p><strong>Age:</strong> {entry.age}</p>
              <p><strong>Gender:</strong> {entry.gender}</p>
              <p><strong>Preference:</strong> {entry.preference}</p>
              <p><strong>Email:</strong> {entry.email}</p>
            </li>
          ))}
        </ul>
      )}
    </div>
  ) : !submitted ? (
    <form
      onSubmit={handleSubmit}
      className="w-full max-w-md mx-auto bg-white p-6 rounded-xl shadow-md space-y-4"
    >
      <input
        type="text"
        name="name"
        placeholder="Your Name"
        required
        onChange={handleChange}
        className="w-full p-2 border border-pink-300 rounded"
      />
      <input
        type="number"
        name="age"
        placeholder="Age"
        required
        onChange={handleChange}
        className="w-full p-2 border border-pink-300 rounded"
      />
      <select
        name="gender"
        required
        onChange={handleChange}
        className="w-full p-2 border border-pink-300 rounded"
      >
        <option value="">Your Gender</option>
        <option value="Male">Male</option>
        <option value="Female">Female</option>
        <option value="Other">Other</option>
      </select>
      <input
        type="text"
        name="preference"
        placeholder="Looking for..."
        required
        onChange={handleChange}
        className="w-full p-2 border border-pink-300 rounded"
      />
      <input
        type="email"
        name="email"
        placeholder="Email Address"
        required
        onChange={handleChange}
        className="w-full p-2 border border-pink-300 rounded"
      />

      <button
        type="submit"
        className="bg-pink-500 hover:bg-pink-600 text-white py-2 px-4 rounded w-full"
      >
        Submit & Continue
      </button>
    </form>
  ) : (
    <div className="max-w-md mx-auto bg-white p-6 rounded-xl shadow-md text-center">
      <h2 className="text-xl font-semibold text-green-600 mb-2">Thank you!</h2>
      <p>We'll review your submission and contact you shortly.</p>

      <a
        href="https://buy.stripe.com/test_5kA28L6vjcU39aU4gg"
        target="_blank"
        rel="noopener noreferrer"
        className="inline-block mt-6 bg-green-500 text-white py-2 px-4 rounded hover:bg-green-600"
      >
        Proceed to Payment
      </a>
    </div>
  )}
</div>

); };

export default App;

