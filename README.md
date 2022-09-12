- 👋 Hi, I’m @1423201170
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
1423201170/1423201170 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.


si7021的相关驱动和函数
/**************************************************************************//**
 * @brief
 * Starts no hold measurement of relative humidity and temperature from a Si70xx sensor.// 开始无保持测量
 * @param[in] i2cspm
 *   The I2C peripheral to use.
 * @param[in] addr
 *   The I2C address of the sensor.
 * @return
 *    @retval SL_STATUS_OK Success
 *    @retval SL_STATUS_TRANSMIT I2C transmission error
 *****************************************************************************/
static sl_status_t sl_si70xx_start_no_hold_measure(sl_i2cspm_t *i2cspm, uint8_t addr, uint8_t command)

{
  sl_status_t retval = SL_STATUS_OK;

  I2C_TransferSeq_TypeDef    seq;
  I2C_TransferReturn_TypeDef ret;
  uint8_t                    i2c_read_data[2];
  uint8_t                    i2c_write_data[1];

  seq.addr  = addr << 1;
  seq.flags = I2C_FLAG_WRITE;
  /* Select command to issue */
  i2c_write_data[0] = command;
  seq.buf[0].data   = i2c_write_data;
  seq.buf[0].len    = 1;
  /* Select location/length of data to be read */
  seq.buf[1].data = i2c_read_data;
  seq.buf[1].len  = 0;

  ret = I2CSPM_Transfer(i2cspm, &seq);

  if (ret != i2cTransferDone) {
    retval = SL_STATUS_TRANSMIT;
  }

  return retval;
}



/**************************************************************************//**
 * @brief
 *  Reads data from the Si70xx sensor.//从 Si70xx 传感器读取数据
 * @param[in] i2cspm
 *   The I2C peripheral to use.
 * @param[in] addr
 *   The I2C address of the sensor.
 * @param[out] data
 *   The data read from the sensor.
 * @return
 *    @retval SL_STATUS_OK Success
 *    @retval SL_STATUS_TRANSMIT I2C transmission error
 *****************************************************************************/
static sl_status_t sl_si70xx_read_data(sl_i2cspm_t *i2cspm, uint8_t addr, uint32_t *data)
{
  I2C_TransferSeq_TypeDef    seq;
  I2C_TransferReturn_TypeDef ret;
  uint8_t                    i2c_read_data[2];

  seq.addr  = addr << 1;
  seq.flags = I2C_FLAG_READ;
  /* Select command to issue */
  seq.buf[0].data = i2c_read_data;
  seq.buf[0].len  = 2;
  /* Select location/length of data to be read */
  seq.buf[1].data = i2c_read_data;
  seq.buf[1].len  = 2;

  ret = I2CSPM_Transfer(i2cspm, &seq);

  if (ret != i2cTransferDone) {
    *data = 0;
    return SL_STATUS_TRANSMIT;
  }

  *data = ((uint32_t) i2c_read_data[0] << 8) + (i2c_read_data[1] & 0xfc);

  return SL_STATUS_OK;
}


