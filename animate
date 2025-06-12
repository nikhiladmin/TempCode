```js
'use client';
import React, { useEffect, useRef, useState } from 'react';


const CircularWave = ({ active=false }) => {
  const canvasRef = useRef(null);
  const audioContextRef = useRef(null);
  const analyserRef = useRef(null);
  const dataArrayRef = useRef(null);
  const animationRef = useRef(null);
  const radiusRef = useRef(30);

  useEffect(() => {
    if (!active) return;

    let stream;

    const setup = async () => {
      stream = await navigator.mediaDevices.getUserMedia({ audio: true });
      const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      const analyser = audioCtx.createAnalyser();
      analyser.fftSize = 128;

      const dataArray = new Uint8Array(analyser.frequencyBinCount);
      const source = audioCtx.createMediaStreamSource(stream);

      source.connect(analyser);

      audioContextRef.current = audioCtx;
      analyserRef.current = analyser;
      dataArrayRef.current = dataArray;

      draw();
    };

    const draw = () => {
      const canvas = canvasRef.current;
      const ctx = canvas.getContext('2d');
      const WIDTH = canvas.width;
      const HEIGHT = canvas.height;

      const render = () => {
        animationRef.current = requestAnimationFrame(render);
        analyserRef.current.getByteFrequencyData(dataArrayRef.current);
        const avg = dataArrayRef.current.reduce((a, b) => a + b, 0) / dataArrayRef.current.length;

        const targetRadius = 30 + avg * 0.8;
        const currentRadius = radiusRef.current;
        const smoothRadius = currentRadius + (targetRadius - currentRadius) * 0.1;
        radiusRef.current = smoothRadius;

        ctx.clearRect(0, 0, WIDTH, HEIGHT);

        const gradient = ctx.createRadialGradient(WIDTH / 2, HEIGHT / 2, 10, WIDTH / 2, HEIGHT / 2, smoothRadius);
        gradient.addColorStop(0, '#4285F4');
        gradient.addColorStop(0.3, '#34A853');
        gradient.addColorStop(0.6, '#FBBC05');
        gradient.addColorStop(1, '#EA4335');

        ctx.beginPath();
        ctx.arc(WIDTH / 2, HEIGHT / 2, smoothRadius, 0, Math.PI * 2);
        ctx.fillStyle = gradient;
        ctx.shadowColor = '#999';
        ctx.shadowBlur = 20;
        ctx.fill();
      };

      render();
    };

    setup();

    return () => {
      cancelAnimationFrame(animationRef.current);
      audioContextRef.current?.close();
      stream?.getTracks().forEach((track) => track.stop());
    };
  }, [active]);

  return (
    <canvas
      ref={canvasRef}
      width={200}
      height={200}
      style={{
        borderRadius: '50%',
        backgroundColor: '#f9fafb',
      }}
    />
  );
};

export default CircularWave;

```

