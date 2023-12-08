#API for cryptocurrency price retrieval and the JavaMail API for email notifications
import javax.mail.;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import java.util.Properties;
import java.util.Timer;
import java.util.TimerTask;

public class CryptoNotifier {

    private static final String API_KEY = "YOUR_Worldapp_PRO_API_KEY";
    private static final String API_SECRET = "YOUR_Worldapp_PRO_API_SECRET";
    private static final String EMAIL_USERNAME = "YOUR_EMAIL@gmail.com";
    private static final String EMAIL_PASSWORD = "YOUR_EMAIL_PASSWORD";
    private static final String TO_EMAIL = "INVESTOR_EMAIL@gmail.com";
    private static final String CRYPTO_SYMBOL = "BTC-USD"; // Change to the desired cryptocurrency

    public static void main(String[] args) {
        Timer timer = new Timer();
        timer.scheduleAtFixedRate(new CheckPriceTask(), 0, 60000); // Check every minute
    }

    private static class CheckPriceTask extends TimerTask {
        @Override
        public void run() {
            double currentPrice = getCryptoPrice();
            double thresholdPrice = 50000.0; // Set your desired threshold price

            if (currentPrice < thresholdPrice) {
                sendNotification(currentPrice);
            }
        }

        private double getCryptoPrice() {
            // Implement logic to fetch cryptocurrency price from Coinbase Pro API
            // Use API_KEY and API_SECRET for authentication
            // Return the current cryptocurrency price
            // Example: return CoinbaseProApi.getPrice(CRYPTO_SYMBOL, API_KEY, API_SECRET);
            return 45000.0; // Placeholder value, replace with actual implementation
        }

        private void sendNotification(double currentPrice) {
            String subject = "Cryptocurrency Alert";
            String message = "The price of " + CRYPTO_SYMBOL + " has dropped below your threshold. Current price: $" + currentPrice;

            sendEmail(subject, message);
        }

        private void sendEmail(String subject, String message) {
            Properties properties = new Properties();
            properties.put("mail.smtp.auth", "true");
            properties.put("mail.smtp.starttls.enable", "true");
            properties.put("mail.smtp.host", "smtp.gmail.com");
            properties.put("mail.smtp.port", "587");

            Session session = Session.getInstance(properties, new Authenticator() {
                @Override
                protected PasswordAuthentication getPasswordAuthentication() {
                    return new PasswordAuthentication(EMAIL_USERNAME, EMAIL_PASSWORD);
                }
            });

            try {
                Message emailMessage = new MimeMessage(session);
                emailMessage.setFrom(new InternetAddress(EMAIL_USERNAME));
                emailMessage.setRecipients(Message.RecipientType.TO, InternetAddress.parse(TO_EMAIL));
                emailMessage.setSubject(subject);
                emailMessage.setText(message);

                Transport.send(emailMessage);
                System.out.println("Notification email sent successfully!");
            } catch (MessagingException e) {
                e.printStackTrace();
            }
        }
    }
}
