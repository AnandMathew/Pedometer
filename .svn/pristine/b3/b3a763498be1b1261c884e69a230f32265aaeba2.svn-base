package com.example.lab2_205_04;

import ca.uwaterloo.sensortoy.LineGraphView;

import java.util.Arrays;

import android.annotation.SuppressLint;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v7.app.ActionBarActivity;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;


@SuppressWarnings("deprecation")
public class MainActivity extends ActionBarActivity 
{
	static LineGraphView graph;
	
	@Override
	protected void onCreate( Bundle savedInstanceState ) 
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		if (savedInstanceState == null) 
		{
			getSupportFragmentManager().beginTransaction()
					.add(R.id.container, new PlaceholderFragment()).commit();
		}

		// Make a graph
		LinearLayout layout = ( ( LinearLayout ) findViewById( R.id.label0 ) );
		
		graph = new LineGraphView( getApplicationContext(), 100, 
				Arrays.asList( "x", "y", "z" ) );
		
	}

	@Override
	public boolean onCreateOptionsMenu( Menu menu ) 
	{
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate( R.menu.main, menu );
		return true;
	}

	@Override
	public boolean onOptionsItemSelected( MenuItem item ) 
	{
		// Handle action bar item clicks here. The action bar will
		// automatically handle clicks on the Home/Up button, so long
		// as you specify a parent activity in AndroidManifest.xml.
		int id = item.getItemId();
		if ( id == R.id.action_settings ) 
		{
			return true;
		}
		return super.onOptionsItemSelected( item );
	}

	public static class PlaceholderFragment extends Fragment 
	{

		public PlaceholderFragment( ) { }

		@SuppressLint("InlinedApi")
		@Override
		public View onCreateView( LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState ) 
		{
			// Initialize necessary views and layout element 
			
			View rootView = inflater.inflate( R.layout.fragment_main, container,
					false );
			
			LinearLayout layout = ( LinearLayout ) rootView.findViewById( R.id.label0 );
			layout.setOrientation( LinearLayout.VERTICAL );
			
			layout.addView( graph );
			graph.setVisibility( View.VISIBLE );
			
			TextView linearAccelerationSensorText = new TextView( rootView.getContext( ) );
			layout.addView( linearAccelerationSensorText );
			
			// Initialize sensor manager and corresponding sensors object
			
			SensorManager sensorManager = ( SensorManager )
					rootView.getContext().getSystemService( SENSOR_SERVICE );
			
			Sensor linearAcceleration =
					sensorManager.getDefaultSensor( Sensor.TYPE_LINEAR_ACCELERATION );
			
			// Initialize sensor event listener
			
			final SensorEventListener linearAccelerationEventListener = 
					new LinearAccelerationEventListener( linearAccelerationSensorText );
			
			// Create a button and add it to he current view
			
			 Button button = ( Button ) rootView.findViewById( R.id.button );
	         button.setOnClickListener( new View.OnClickListener() 
	         	{		
	        	 	public void onClick(View v) 
	        	 	{
	        	 		graph.purge();
	        	 		( ( LinearAccelerationEventListener ) linearAccelerationEventListener ).Clear();
	        	 	}
	         	}
	         );
			
			// Initiate listener
			
			sensorManager.registerListener( linearAccelerationEventListener, 
					linearAcceleration, SensorManager.SENSOR_DELAY_FASTEST );
			
			
			return rootView;
		}
	}
		
	
	static public class LinearAccelerationEventListener implements SensorEventListener
	{
		
	    public TextView output;
		float recordValueX;
		float recordValueY;
		double recordValueZ;
		public int steps;
		
		public LinearAccelerationEventListener( TextView outputView )
		{
			output = outputView;
			recordValueX = 0;
			recordValueY = 0;
			recordValueZ = 0;
			steps = 0;
		}
		
		public class FiniteStateMachine
		{
			int state = 0;
			
			public boolean stepCompleted()
			{
				return( state == 2 );
			}
			
			public void stepCounter( float input )
			{
				switch( state )
				{
					case 0:
						
						if( input >= Math.abs( 3.875 ) ) 
							state = 1;
						
						else 
							state = 0;
						
						break;
					
					case 1:
						
						if (input < Math.abs(3.875)) 
							state = 2;
						
						else 
							state = 1;
						
						break;
						
					case 2:
						
						state = 0;
				}		
			}
		}
	    
		FiniteStateMachine pedometer = new FiniteStateMachine();
	    
	    @Override
		public void onSensorChanged( SensorEvent se ) 
		{
			if( se.sensor.getType( ) == Sensor.TYPE_LINEAR_ACCELERATION  )
			{
				float[] temp = lowpassFilter( se.values, ( float ) 0.2 );
				
				graph.addPoint( temp );
				
				pedometer.stepCounter( ( float ) ( se.values[2] ) );
				
				if( pedometer.stepCompleted( ) ) 
					steps++;
				
				String reading = String.valueOf( 
					"Lineaer acceleration on the x-axis: " + se.values[ 0 ] + "\n" +
					"Lineaer acceleration on the x-axis: " + se.values[ 1 ] + "\n" +
					"Lineaer acceleration on the x-axis: " + se.values[ 2 ] + "\n" +
					"\n" +
					"Number of steps" + "\n" + steps
				);
				
				output.setText( reading );
			}
		}
	    
	    private float[] lowpassFilter( float[] inputArray, float alpha ) 
		{
			float[] outputArray = new float[ inputArray.length ];
			
			outputArray[ 0 ] = 0;
			for(int i = 1; i < inputArray.length; i++) 
			{
				outputArray[ i ] = 
						alpha * inputArray[ i ] + ( 1 - alpha ) * outputArray[ i - 1];
			}
			return outputArray;
		}
		
	    public void Clear()
		{
			this.steps = 0;
		}
	    
		@Override
		public void onAccuracyChanged(Sensor sensor, int accuracy) { }	
	}
}