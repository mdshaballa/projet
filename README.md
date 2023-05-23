# projet

import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.client.RestTemplate;

import static org.mockito.Mockito.*;

public class StockServiceTest {
  
  @Mock
  private WebClientConfig webClientConfig;
  
  @Mock
  private RestTemplate restTemplate;
  
  @Test
  public void testGetStock() {
    // Création des objets requis pour le test
    HttpHeaders headers = new HttpHeaders();
    headers.setAccept(List.of(MediaType.APPLICATION_JSON));
    
    // Mock de la méthode getLeaWebClient()
    when(webClientConfig.getLeaWebClient()).thenReturn(restTemplate);
    
    // Création de la réponse simulée
    ResponseEntity<String> mockResponse = ResponseEntity.ok("Stock data");
    
    // Mock de la méthode exchange() de RestTemplate
    when(restTemplate.exchange(
      Mockito.anyString(),
      eq(HttpMethod.GET),
      eq(new HttpEntity<>(headers)),
      eq(String.class),
      Mockito.anyMap()
    )).thenReturn(mockResponse);
    
    // Création de l'objet à tester
    StockService stockService = new StockService(webClientConfig);
    
    // Exécution de la méthode à tester
    stockService.getStock();
    
    // Vérification des appels de méthodes
    verify(webClientConfig, times(1)).getLeaWebClient();
    verify(restTemplate, times(1)).exchange(
      Mockito.anyString(),
      eq(HttpMethod.GET),
      eq(new HttpEntity<>(headers)),
      eq(String.class),
      Mockito.anyMap()
    );
    // Ajoutez ici d'autres assertions ou vérifications supplémentaires en fonction de votre cas d'utilisation
  }
}



import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.retry.RetryCallback;
import org.springframework.retry.RetryContext;
import org.springframework.retry.RetryTemplate;
import org.springframework.web.client.RestTemplate;

import static org.mockito.Mockito.*;

public class StockServiceTest {
  
  @Mock
  private WebClientConfig webClientConfig;
  
  @Mock
  private RestTemplate restTemplate;
  
  @Mock
  private RetryTemplate retryTemplate;
  
  @Test
  public void testGetStock() {
    // Création des objets requis pour le test
    HttpHeaders headers = new HttpHeaders();
    headers.setAccept(List.of(MediaType.APPLICATION_JSON));
    
    // Mock de la méthode getLeaWebClient()
    when(webClientConfig.getLeaWebClient()).thenReturn(restTemplate);
    
    // Création de la réponse simulée
    ResponseEntity<String> mockResponse = ResponseEntity.ok("Stock data");
    
    // Mock de la méthode exchange() de RestTemplate
    when(restTemplate.exchange(
      Mockito.anyString(),
      eq(HttpMethod.GET),
      eq(new HttpEntity<>(headers)),
      eq(String.class),
      Mockito.anyMap()
    )).thenReturn(mockResponse);
    
    // Mock du RetryCallback
    when(retryTemplate.execute(Mockito.any(RetryCallback.class))).thenAnswer(invocation -> {
      RetryCallback<Object, Exception> callback = invocation.getArgument(0);
      return callback.doWithRetry(mock(RetryContext.class));
    });
    
    // Création de l'objet à tester
    StockService stockService = new StockService(webClientConfig, retryTemplate);
    
    // Exécution de la méthode à tester
    stockService.getStock();
    
    // Vérification des appels de méthodes
    verify(webClientConfig, times(1)).getLeaWebClient();
    verify(restTemplate, times(1)).exchange(
      Mockito.anyString(),
      eq(HttpMethod.GET),
      eq(new HttpEntity<>(headers)),
      eq(String.class),
      Mockito.anyMap()
    );
    verify(retryTemplate, times(1)).execute(Mockito.any(RetryCallback.class));
    // Ajoutez ici d'autres assertions ou vérifications supplémentaires en fonction de votre cas d'utilisation
  }
}
