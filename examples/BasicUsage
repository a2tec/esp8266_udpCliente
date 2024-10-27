#include <ESP8266WiFi.h>
#include <WiFiUdp.h>

//****************************************************************************************************

int PECA = 3;

//****************************************************************************************************

WiFiUDP Udp;

unsigned int Porta = 9999;

char id[20];
char rede[20] = "HOTSPOT";
char *pwd = "102030";

int SERVIDOR = PECA - 1;

IPAddress ipadd(PECA_SERVIDOR, 168, 100, PECA);
IPAddress ipser(PECA_SERVIDOR, 168, 100, SERVIDOR);
IPAddress ipmsk(255, 255, 255, 0);

//****************************************************************************************************

String r;

boolean RX = false;
boolean WIFI_Conectado = false;
boolean Modo_Teste = false;

//****************************************************************************************************

void serialEvent();
void Conecta_WIFI();

//****************************************************************************************************
void setup() {
   delay(5000);

   Serial.begin(115200);
   while (!Serial) {};

   Serial.println("\n");
   Serial.println("SERIAL OK");

   delay( 100 );

   WIFI_Conectado = false;
  
   Conecta_WIFI();
}

//****************************************************************************************************
void loop() {
   serialEvent();

   if (WiFi.status() != WL_CONNECTED) {
      if ( WIFI_Conectado ) {
         WIFI_Conectado = false;
         Serial.println( "-----DESCONECTADO -----" );
      }

      //Conecta_WIFI();
   } else {
      if ( !WIFI_Conectado ) {
         WIFI_Conectado = true;

         Serial.println( "-----------------------" );
         Serial.print("REDE: "); Serial.println( id );
         Serial.print("IP  : "); Serial.println( WiFi.localIP() );
         Serial.print("UDP : "); Serial.println( Porta );
         Serial.println( "-----------------------" );
      }

      if ( Udp ) {
         if ( RX ) {
            if (r == "") return;

            Udp.beginPacket(ipser, Porta);

            int i = r.length();
            char txt[i];

            r.toCharArray(txt, i);

            Udp.write( txt );
            Udp.endPacket();

            if ( Modo_Teste ) {
               Serial.print(ipser); Serial.println("=" + r);
            }

            RX = false;
         }
      }
   }
}

//****************************************************************************************************
void serialEvent() {
  if (Serial.available()) {
    r = Serial.readStringUntil('\n');
    r.toUpperCase();

    if (r.substring(0, 4) == "<MT>") {
      Modo_Teste = ~Modo_Teste;

      if ( Modo_Teste ) {
        Serial.println( "MODO TESTE: LIGADO" );
      } else {
        Serial.println( "MODO TESTE: DESLIGADO" );
      }

      return;
    }

    RX = true;
  }
}

//****************************************************************************************************
void Conecta_WIFI() {   
   WiFi.disconnect();

   delay( 100 );

   sprintf(id, "%s %03i", rede, SERVIDOR);

   WiFi.mode(WIFI_STA);
   WiFi.begin(id);
   WiFi.config(ipadd, ipser, ipmsk);

   int i = 0;
   while (WiFi.status() != WL_CONNECTED) {
      Serial.print(".");
      delay(250);

      i++;
      if (i > 23) {
         i = 0;
         Serial.println();
      }
   }

   if (WiFi.status() != WL_CONNECTED) {
      Serial.println("DESCONECTADO");

      delay(3000);
      ESP.reset();
      delay(5000);

      return;
   }

   WIFI_Conectado = true;

   Serial.println();

   Udp.begin(Porta);
   if ( Udp ) {
      Serial.println( "-----------------------" );
      Serial.print("REDE: "); Serial.println( id );
      Serial.print("IP  : "); Serial.println( WiFi.localIP() );
      Serial.print("UDPP: "); Serial.println( Porta );
      Serial.println( "-----------------------" );
   }
}

