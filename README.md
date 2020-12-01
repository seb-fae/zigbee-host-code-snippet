# zigbee-example

## Z3Host: Send Custom command to NCP:

### 1) Add xncp plugin on ncp side
### 2) Add this code to Z3Host_callback.c

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

## Set Manufacturing Ctune in User/Lockbit page from Z3Host

emberCommandEntryAction("setMfgCtune", setMfgCtune, "u", ""),

static void setMfgCtune(void)
{
  uint16_t ctune = (uint8_t)emberSignedCommandArgument(0);
  EmberStatus status = ezspSetMfgToken(EZSP_MFG_CTUNE, 2, (uint8_t *) &ctune);
  emberAfCorePrintln("status 0x%x\n", status);
}

## Set a Ctune in NCP CMU register from Z3Host

emberCommandEntryAction("setCtune", setCtune, "u", ""),

static void setCtune(void)
{
  uint16_t ctune = (uint16_t)emberSignedCommandArgument(0);
  emberAfCorePrintln("ctune 0x%x\n", ctune);
  EmberStatus status = ezspSetConfigurationValue(EZSP_CONFIG_CTUNE_VALUE, ctune);
  emberAfCorePrintln("status 0x%x\n", status);
}

## Read CTune from Z3Host:

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
