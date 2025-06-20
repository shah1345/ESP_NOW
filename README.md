# ESP_NOW


  // Ensure ESP-NOW uses same channel as Wi-Fi
  uint8_t wifiChannel = WiFi.channel();
  esp_wifi_set_promiscuous(true);
  esp_wifi_set_channel(wifiChannel, WIFI_SECOND_CHAN_NONE);
  esp_wifi_set_promiscuous(false);
