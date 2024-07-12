I haven't got to test and prove that it works but, here it goes:

### Requirements
*Hardware:* 
1. (Old) Google Pixel Phone
2.  (New) Any other phone

*Software:*
1. `Syncthing` on Both Phones

### Hypothesis
You should be able to sync your photos from your new phone to your old phone periodically when you are in your local network and the old phone backs up the photos using google photos which will occupy no storage on google drive since it is uploaded from a Pixel.


### Procedure
1. Install `syncthing` on both devices.
2. Pair two phones in `syncthing` (Both phones need to be in the same local network.)
3. Setup to sync the `DCIM` folder from New Phone to Old Pixel Phone
4. Disable google photos backup on New phone and Only leave it enabled on the old phone.
5. Put the Old Pixel Phone plugged in and connected to the network. (Basically a NAS)
6. The `syncthing` daemon should be running on both devices perpetually. (Disable Battery Optimizations for the `syncthing` app on both devices).
7. The photos should sync when both devices are in the same network, but the storage of the Old Pixel Phone will be occupied hence periodically click on the `free up space on this device` option in google photos on the Old Pixel Phone to prevent filling up the space and stopping the sync.
8. Enjoy unlimited photos and video storage.

> Here "An Old Pixel Phone" can be a phone with a Custom Pixel OS ported ROM with Unlimited Photos enabled or a rooted android with equivalent module installed.