# ESP_NOW
#include "esp_wifi.h" 

  // Ensure ESP-NOW uses same channel as Wi-Fi
  uint8_t wifiChannel = WiFi.channel();
  esp_wifi_set_promiscuous(true);
  esp_wifi_set_channel(wifiChannel, WIFI_SECOND_CHAN_NONE);
  esp_wifi_set_promiscuous(false);









  #######################





  void OnDataRecv(const uint8_t * mac, const uint8_t *incomingData, int len) {
  memcpy(&receivedData, incomingData, sizeof(receivedData));
  memcpy(senderMac, mac, 6);  // Save sender MAC for reply

  Serial.println("Data received:");
  Serial.print("RF Tag: "); Serial.println(receivedData.rf_tag);
  Serial.print("Nozzle: "); Serial.println(receivedData.nozzle_no);
  Serial.print("Time: "); Serial.println(receivedData.time_str);

  // Optional: check if sender is already a known peer
  if (!esp_now_is_peer_exist(mac)) {
    esp_now_peer_info_t peerInfo = {};
    memcpy(peerInfo.peer_addr, mac, 6);
    peerInfo.channel = WiFi.channel();  // Match current Wi-Fi channel
    peerInfo.encrypt = false;

    if (esp_now_add_peer(&peerInfo) != ESP_OK) {
      Serial.println("Failed to add sender as peer!");
      return;
    } else {
      Serial.println("Sender peer added.");
    }
  }

  // Prepare response
  replyData.status = true;
  snprintf(replyData.message, sizeof(replyData.message), "Approved Nozzle %d", receivedData.nozzle_no);

  // Send response back to sender
  esp_err_t result = esp_now_send(mac, (uint8_t *)&replyData, sizeof(replyData));
  if (result == ESP_OK) {
    Serial.println("Response sent successfully.");
  } else {
    Serial.print("Failed to send response. Error code: ");
    Serial.println(result);
  }
}

