# zigbee-host-example

## Send Custom command to NCP:

### 1) Add xncp library plugin to your NCP project
### 2) Add this code to Z3Host_callback.c

```c
#define MAXRSPLEN 16

uint8_t cmd[1] = {1};
uint8_t rsp[MAXRSPLEN];
uint8_t rsplen = MAXRSPLEN;

static void sendCustom(void)
{
  EmberStatus status;
  status = ezspCustomFrame(sizeof(cmd), cmd, &rsplen, rsp);

  emberAfCorePrintln("rsp len %d", rsplen);
  for (uint8_t i = 0; i< rsplen; i++)
	  emberAfCorePrintln("%x", rsp[i]);
}
```
## Set Manufacturing Ctune in User/Lockbit page from Host
```c
emberCommandEntryAction("setMfgCtune", setMfgCtune, "u", ""),

static void setMfgCtune(void)
{
  uint16_t ctune = (uint8_t)emberSignedCommandArgument(0);
  EmberStatus status = ezspSetMfgToken(EZSP_MFG_CTUNE, 2, (uint8_t *) &ctune);
  emberAfCorePrintln("status 0x%x\n", status);
}
```

### warning: To perform multiple write, you need to use "commander device pageerase --region @userdata" before each write

## Set a Ctune in NCP CMU register from Host

emberCommandEntryAction("setCtune", setCtune, "u", ""),
```c
static void setCtune(void)
{
  uint16_t ctune = (uint16_t)emberSignedCommandArgument(0);
  EmberStatus status = ezspSetConfigurationValue(EZSP_CONFIG_CTUNE_VALUE, ctune);
  emberAfCorePrintln("status 0x%x\n", status);
}
```
### warning: you must not be on a network to use this command. 

## Read CTune from Host:

```c
static void getCtune(void)
{
	uint16_t ctune;
	ezspGetConfigurationValue(EZSP_CONFIG_CTUNE_VALUE, &ctune);
	emberAfCorePrintln("ctune 0x%x\n", ctune);
	
	uint16_t mfgctune;
	uint8_t res = ezspGetMfgToken(EZSP_MFG_CTUNE, (uint8_t *) &mfgctune);
	emberAfCorePrintln("ctuneMfg 0x%x\n", mfgctune);
}
```
