# LAB-8---Threads-AsyncTask-et-Handler



## le code logique :  

    <Button
        android:id="@+id/btn_action_async"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Calcul via AsyncTask"
        android:layout_marginTop="10dp"
        android:backgroundTint="#2196F3"/>

    <Button
        android:id="@+id/btn_verification_ui"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Test de Réactivité (Toast)"
        android:layout_marginTop="10dp"
        android:backgroundTint="#FF9800"/>

</LinearLayout>
package com.example.labthreadsasynctask;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.AsyncTask;
import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.ProgressBar;
import android.widget.TextView;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

/**
 * Développement Mobile - Travail Pratique
 * Analyse de l'asynchronisme par Abdelghafour
 */
public class MainActivity extends AppCompatActivity {

    private TextView afficheurStatut;
    private ProgressBar maBarreProgression;
    private ImageView zoneImage;
    private Handler monHandlerPrincipal;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Liaison des composants
        afficheurStatut = findViewById(R.id.label_info_statut);
        maBarreProgression = findViewById(R.id.barre_progression_travail);
        zoneImage = findViewById(R.id.zone_affichage_image);

        // Handler pour la communication Thread -> UI Thread
        monHandlerPrincipal = new Handler(Looper.getMainLooper());

        // 1. Bouton Toast : Vérifie que l'UI reste fluide
        findViewById(R.id.btn_verification_ui).setOnClickListener(v -> 
            Toast.makeText(this, "Réactivité OK - Abdelghafour", Toast.LENGTH_SHORT).show()
        );

        // 2. Action via Thread classique
        findViewById(R.id.btn_action_thread).setOnClickListener(v -> chargerImageLongue());

        // 3. Action via AsyncTask
        findViewById(R.id.btn_action_async).setOnClickListener(v -> new TacheCalculLourd().execute());
    }

    private void chargerImageLongue() {
        afficheurStatut.setText("Thread : Chargement lancé...");
        maBarreProgression.setVisibility(View.VISIBLE);

        new Thread(() -> {
            try {
                Thread.sleep(1500); // Simulation délai
            } catch (InterruptedException e) { e.printStackTrace(); }

            Bitmap img = BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher);

            // Retour vers l'UI Thread obligatoire
            monHandlerPrincipal.post(() -> {
                zoneImage.setImageBitmap(img);
                maBarreProgression.setVisibility(View.INVISIBLE);
                afficheurStatut.setText("Thread : Terminé !");
            });
        }).start();
    }

    private class TacheCalculLourd extends AsyncTask<Void, Integer, String> {
        @Override
        protected void onPreExecute() {
            maBarreProgression.setVisibility(View.VISIBLE);
            afficheurStatut.setText("AsyncTask : Calcul démarré...");
        }

        @Override
        protected String doInBackground(Void... voids) {
            for (int i = 0; i <= 100; i++) {
                // Simulation calcul lourd
                for (int j = 0; j < 300000; j++) { /* Calcul fictif */ }
                publishProgress(i); // Mise à jour UI demandée
            }
            return "Succès";
        }

        @Override
        protected void onProgressUpdate(Integer... progress) {
            maBarreProgression.setProgress(progress[0]);
        }

        @Override
        protected void onPostExecute(String s) {
            maBarreProgression.setVisibility(View.INVISIBLE);
            afficheurStatut.setText("AsyncTask : Calcul terminé !");
        }
    }
}


<img width="454" height="941" alt="image" src="https://github.com/user-attachments/assets/4adbef23-44d0-4f6b-bbe3-fd48af0b8980" />



<img width="454" height="941" alt="image" src="https://github.com/user-attachments/assets/c20709a7-bfc8-49d3-b3f7-bb4212daddde" />


<img width="454" height="941" alt="image" src="https://github.com/user-attachments/assets/87b7f651-a797-455c-8caa-0b052a3194f8" />


<img width="454" height="941" alt="image" src="https://github.com/user-attachments/assets/41cea799-c2e0-4d0c-8695-760cc5720ee4" />
