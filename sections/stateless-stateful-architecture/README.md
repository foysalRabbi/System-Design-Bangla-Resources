## High Traffic in a Stateful Architecture

আমরা জানি Stateful Archtecture ডেটা সার্ভার স্টোর করে রাখে। এখন যদি request এর পরিমাণ বেশি হয় তখন কোনো এক কারণে সার্ভারে কোনো ত্রুটি হওয়ার ফলে ডেটা নষ্ট হয়ে যেতে পারে, তখন ডেটা recover করার সুযোগ থাকে না।

<p align="center">
  <img src="./images/stateful-1.png" alt="Stateful pic">
</p>

ডেটা নষ্ট হওয়ার আগে।

<p align="center">
  <img src="./images/stateful-2.png" alt="Stateful pic">
</p>

অতিরিক্ত Client Request এর ফলে Client 1 এর Data নষ্ট হওয়ার পর, সার্ভারে Client 1 এর ডেটা আর নাই।

## Scalability - Stateful and Stateless Architecture

উপরের যে issue নিয়ে বলা হলো তা Stateful Architecture এ হয়ে থাকে। একই issue Stateless Architecture যেরকম কাজ করে,

<p align="center">
  <img src="./images/stateless-1.png" alt="Stateless pic">
</p>

একাধিক user সার্ভারে call দেয়া শুরু করলো, আমাদের সার্ভারের রিসোর্স কম সেজন্য আমাদের সার্ভার একটি সময় ক্র্যাশ করবে। এই সমস্যা সমাধানের জন্য আমরা Load Balancer দিয়ে Horizontal Scaling ব্যবহার করতে পারি।

<p align="center">
  <img src="./images/stateless-2.png" alt="Stateless pic">
</p>

যেহেতু Stateless Architecture এ সার্ভারের মধ্যে কোনো প্রকারের ডেটা কিংবা ইনফরমেশন সংরক্ষন হচ্ছে না, সেহেতু ক্লায়েন্ট যেকোন সার্ভার থেকে ডেটা পেলেই হবে। এরকম আমরা Stateless Architecture এ খুব সহজে Scale করতে পারি।

একই সমস্যা আমরা Stateful Architecture এ একইভাবে সমাধান করতে পারবো না, কারণ এই Architecture এ সার্ভারের ভিতর ক্লায়েন্টের ইনফরমেশন/ডেটা সংরক্ষন থাকে। তাহলে কিভাবে সমাধান করবো?

## How to Scale Stateful Architecture

এই সমস্যা সমাধানের জন্য আমাদের কাছে দুটি options আছে। Message Queue এবং Pub-Sub।

Pub-sub দিয়ে যদি আমরা সমাধান করি, তাহলে প্রথমে আমাদের বুজতে হবে pub-sub কি। Pub-sub মানে হলো Publish and Subscribe pattern। Publisher ডেটা পাঠাবে এবং সেই ডেটা বিভিন্ন সাবস্ক্রাইবার যে সাবস্ক্রাইব করে রাখবে তাদের কাছে চলে যাবে। Redis ভালোভাবে Publish and Subscribe pattern provide করে থাকে।

<p align="center">
  <img src="./images/stateful-3.png" alt="Stateful pic">
</p>

উপরের ছবি অনুযায়ী আমরা ধরে রাখি user 1 এর ইনফরমেশন সার্ভার 1 এর কাছে রয়েছে, user 2 এর ইনফরমেশন সার্ভার 2 এর কাছে এবং user 3 এর ইনফরমেশন সার্ভার 3 এ রয়েছে। user 1 যখন ডেটা সার্ভার 1 এ পাঠাবে তখন সার্ভার 1, publisher মানে redis এ সেই ডেটা publish করবে, এখন redis publisher এ যদি সার্ভার 2 এবং 3 subscribe করে রাখে তাহলে এরাও সেই ডেটা পেয়ে যাবে। তারপর সার্ভার 2 এবং ৩, user 2 এবং 3 এর মধ্যে ডেটা পাঠিয়ে দিবে। 

এরকম আমরা Stateful Architecture স্কেল করতে পারি। 