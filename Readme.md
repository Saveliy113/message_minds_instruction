# How to start Message-Minds API and Starter
To start Message-Minds API with socket.io you need to go through the following steps:

- Uncomment this section of code in _server.js_ file (lines 58 to 75):   
```
// Socket logic
const io = new Server(server, {
  cors: {
    origin: [`http://127.0.0.1:5173`,`http://localhost:5173`, `https://dashboard.messageminds.com`],
  }
});
io.adapter(createAdapter());
setupWorker(io);

io.on("connection", (socket) => {
  console.log('Socket has been initialized.');
  console.log(`Socket ID: `, socket.id);
  
  socket.on("create-wpp-session", (obj, room) => {
    console.log('object', obj)
   
    WhatsappController.createWhatsappSessionBySocket({body: obj}, socket);
  })
});
```
- If _pm2_ is already installed on your computer, remove it first:  
`npm remove -g pm2`.

- In the last update we have added socket.io cluster adapter in the API for correct working of socket.io logic. Thats why you need to install and use _@socket.io/pm2_ package instead of common pm2:  
 `npm install -g @socket.io/pm2`
- Now start multiple API nodes:   
  `pm2 start -i 2 --name server`
- After starting apis, they are ready to handle events from the client by socket.io. You can test it via postman. Create socket.io connection, connect to `http://localhost:3000`, set argument with type JSON, send an event __create_wpp_session__. JSON example:  
```
{
  "name": "test",
  "phone": 77776381517,
  "email": test@gmail.com
}
```
![postman_1](https://github.com/Saveliy113/message_minds_instruction/blob/master/screenshots/instr1.jpg)   
![postman_2](https://github.com/Saveliy113/message_minds_instruction/blob/master/screenshots/instr2.jpg)
- You will receive a generated QR-code in terminal. Scan it with your phone to authenticate and create session.

To start starter move to a project folder, open a terminal and execute this command:  
`pm2 start server.js --name starter`

If you need to test recreating whatsapp sessions functionality by starter, reload servers by executing command: `pm2 reload server`. And then reload a starter: `pm2 reload starter`. It will recreate sessions for those clients, who has an online status and hasn't session in any server.
