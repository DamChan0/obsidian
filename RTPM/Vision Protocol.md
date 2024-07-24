
> [!NOTE] vision protocol 
> 
[[RTPM]] 을 사용하는 tc_nn_app의 데이터 전송 프로토콜


# 기능

| function **name**    |                                                                                                                                   |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Initialization**   | int Vision_API_Initialization(const vision_user_config_t *pUserConfig, void **pHandle);                                           |
| **Deinitialization** | int Vision_API_Deinitialization(void **pHandle);                                                                                  |
| send Buffer 등록       | int Vison_API_RegisterStreamSendBuffer(const void *pHandle, void *pBuffer, uint32_t length);                                      |
| recive Buffer 등록     | int Vision_API_RegisterStreamRecvBuffer(const void *pHandle, void *pBuffer, uint32_t length);                                     |
| message 보내기 (작은 데이터) | int Vision_API_SendMessage(const void *pHandle, const void *pBuffer, uint32_t length, int16_t timeout_ms);                        |
| message 받기 (작은 데이터)  | int Vision_API_RecvMessage(const void *pHandle, void *pBuffer, uint32_t length, int16_t timeout_ms);                              |
| message 사이즈 및 아이디 체크 | int Vision_API_PeekMessage(const void *pHandle, void *pBuffer, uint32_t length);                                                  |
| Buffer 사용            | int Vision_API_GetStreamSendBuffer(const void *pHandle, vision_stream_info_t **pStreamInfo, uint8_t *pIndex, int16_t timeout_ms); |
| stream 보내기           | int Vision_API_SendStream(const void *pHandle, const vision_stream_info_t *pStreamInfo, uint8_t index);                           |
| stream 받기            | int Vision_API_RecvStream(const void *pHandle, vision_stream_info_t **pStreamInfo, uint8_t *pIndex, int16_t timeout_ms);          |
| stream 사이즈 및 아이디 체크  | int Vision_API_PeekStream(const void *pHandle, vision_stream_info_t **pStreamInfo, uint8_t *pIndex);                              |
| stream 해제            | int Vision_API_ReleaseStreamRecvBuffer(const void *pHandle, const vision_stream_info_t *pStreamInfo, uint8_t index);              |
| 연결 / 연결 해제           | int Vision_API_Connection(void *pHandle);<br>int Vision_API_Disconnection(void *pHandle);                                         |









