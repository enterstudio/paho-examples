# Connecting using TLS in the Java Client

## This is the first example for the Eclipse Paho Examples Site

This would be an interesting description about how connecting using TLS is a great idea!

## Code

```
import java.io.InputStream;
import java.security.KeyStore;
import java.security.SecureRandom;
import java.security.cert.Certificate;
import java.security.cert.CertificateFactory;

import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.TrustManagerFactory;

import org.eclipse.paho.client.mqttv3.MqttClient;
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;
import org.eclipse.paho.client.mqttv3.MqttException;
import org.eclipse.paho.client.mqttv3.MqttMessage;
import org.eclipse.paho.client.mqttv3.persist.MemoryPersistence;

public class App
{
    public static void main( String[] args )
    {

        String topic        = "MQTT Examples";
        String content      = "Message from MqttPublishSample";
        int qos             = 2;
        String broker       = "ssl://iot.eclipse.org:8883";
        String clientId     = "JavaSample";
        String certificateName = "iot.eclipse.org.crt";
        MemoryPersistence persistence = new MemoryPersistence();


        try {
            MqttClient sampleClient = new MqttClient(broker, clientId, persistence);
            MqttConnectOptions connOpts = new MqttConnectOptions();
            SSLSocketFactory socketFactory = getSocketFactory(certificateName);
            connOpts.setSocketFactory(socketFactory);
            connOpts.setCleanSession(true);
            System.out.println("Connecting to broker: "+broker);
            sampleClient.connect(connOpts);
            System.out.println("Connected");
            System.out.println("Publishing message: "+content);
            MqttMessage message = new MqttMessage(content.getBytes());
            message.setQos(qos);
            sampleClient.publish(topic, message);
            System.out.println("Message published");
            sampleClient.disconnect();
            System.out.println("Disconnected");
            System.exit(0);
        } catch(MqttException me) {
            System.out.println("reason "+me.getReasonCode());
            System.out.println("msg "+me.getMessage());
            System.out.println("loc "+me.getLocalizedMessage());
            System.out.println("cause "+me.getCause());
            System.out.println("excep "+me);
            me.printStackTrace();
        } catch (Exception ex) {
          ex.printStackTrace();
        }
    }

    private static SSLSocketFactory getSocketFactory(String certificateName) throws Exception {
      // Load the certificate from src/main/resources and create a Certificate object
  		InputStream certStream = App.class.getClassLoader().getResourceAsStream(certificateName);
  		CertificateFactory certFactory = CertificateFactory.getInstance("X509");
      Certificate certificate =  certFactory.generateCertificate(certStream);
      SSLContext sslContext = SSLContext.getInstance("TLS");
      TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
  		KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
  		keyStore.load(null);
  		keyStore.setCertificateEntry("alias", certificate);
  		trustManagerFactory.init(keyStore);
  		sslContext.init(null, trustManagerFactory.getTrustManagers(), new SecureRandom());
  		return sslContext.getSocketFactory();
    }



}
```
