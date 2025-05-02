# Alumni-Film-Room
Alumni Film Room is a platform that connects alumni with film from their playing days.
import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { motion } from "framer-motion";
import Image from "next/image";
import { db } from "@/lib/firebase";
import { collection, doc, getDoc } from "firebase/firestore";

export default function AlumniFilmRoom() {
  const [code, setCode] = useState("");
  const [videos, setVideos] = useState<any[] | null>(null);
  const [error, setError] = useState("");

  const handleSubmit = async () => {
    const docRef = doc(collection(db, "accessCodes"), code);
    const docSnap = await getDoc(docRef);

    if (docSnap.exists()) {
      setVideos(docSnap.data().videos);
      setError("");
    } else {
      setError("Invalid access code. Please try again.");
      setVideos(null);
    }
  };

  return (
    <div className="min-h-screen bg-gray-100 p-6 flex flex-col items-center justify-center">
      <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} className="mb-6">
        <Image src="/Logo.png" alt="Alumni Film Room Logo" width={120} height={120} />
      </motion.div>

      <h1 className="text-3xl font-bold mb-4">Alumni Film Room</h1>

      {!videos ? (
        <Card className="p-4 w-full max-w-md">
          <CardContent>
            <Input
              placeholder="Enter your access code"
              value={code}
              onChange={(e) => setCode(e.target.value)}
              className="mb-4"
            />
            <Button className="w-full" onClick={handleSubmit}>Access Film</Button>
            {error && <p className="text-red-500 text-sm mt-2">{error}</p>}
          </CardContent>
        </Card>
      ) : (
        <div className="w-full max-w-xl">
          <h2 className="text-2xl font-semibold mb-4">Available Films:</h2>
          <ul className="space-y-2">
            {videos.map((video, index) => (
              <li key={index} className="bg-white p-4 rounded-xl shadow-md">
                <a href={video.url} target="_blank" rel="noopener noreferrer" className="text-blue-600 underline">
                  🎥 {video.title}
                </a>
              </li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}
