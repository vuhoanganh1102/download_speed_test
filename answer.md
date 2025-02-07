```
let downloadedAmount = 0;
let validDownloadedAmount = 0; // Track data used for speed calculation
let startTime = Date.now();
let firstValidTime = null;
let lastValidTime = null;

// Download simulator callback
downloadSimulator((status, receivedPacketByteSize) => {
const currentTime = Date.now();
const elapsedTime = (currentTime - startTime) / 1000; // Convert to seconds
downloadedAmount += receivedPacketByteSize;

// Ignore the first 2 seconds and last 2 seconds
if (elapsedTime >= 2) {
if (firstValidTime === null) {
firstValidTime = currentTime; // Mark when the valid period starts
}
validDownloadedAmount += receivedPacketByteSize;
lastValidTime = currentTime; // Continuously update until end
}

console.log(
`status: ${status}, Packet Size: ${(receivedPacketByteSize / 1000).toFixed(2)} kB, Downloaded: ${(downloadedAmount / 1000).toFixed(2)} kB`
);

if (status === "done") {
if (firstValidTime !== null && lastValidTime !== null) {
const validDuration = (lastValidTime - firstValidTime) / 1000;
const speed = validDownloadedAmount / validDuration;
console.log(`Download Speed (ignoring first & last 2s): ${(speed / 1000).toFixed(2)} kB/s`);
} else {
console.log("Not enough data for accurate speed calculation.");
}
}
});

// Do not change below
function downloadSimulator(cb) {
return new Promise(async (resolve) => {
const totalAmount = 10 _ 1000 _ 1000; // 50 MB
let downloadedAmount = 0;
let status = "data";

    while (downloadedAmount < totalAmount) {
      const timeOut = Math.random() * 300;
      await wait(timeOut);

      let packetSize = Math.random() * 1000 * 1000;
      if (packetSize + downloadedAmount >= totalAmount) {
        packetSize = totalAmount - downloadedAmount;
        status = "done";
      }

      cb(status, packetSize);
      downloadedAmount += packetSize;
    }
    resolve();

});
}

async function wait(ms) {
return new Promise((resolve) => {
setTimeout(resolve, ms);
});
}
```
