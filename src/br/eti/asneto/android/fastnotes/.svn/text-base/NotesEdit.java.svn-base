package br.eti.asneto.android.fastnotes;

import android.app.Activity;
import android.database.Cursor;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.widget.Button;
import android.widget.EditText;
import br.eti.asneto.android.fastnotes.db.NotesDbAdapter;
import br.eti.asneto.android.fastnotes.util.ComponentUtil;

public class NotesEdit extends Activity {
	
	private EditText mTitleText;
	private EditText mBodyText;
	private Long mRowId;
	private NotesDbAdapter mDbHelper;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		
		requestWindowFeature(Window.FEATURE_CUSTOM_TITLE);
	    setContentView(R.layout.note_edit);
	    getWindow().setFeatureInt(Window.FEATURE_CUSTOM_TITLE, R.layout.title);
		
	    mDbHelper = new NotesDbAdapter(this);
		mTitleText = ComponentUtil.get(this, R.id.title);
		mBodyText = ComponentUtil.get(this, R.id.body);
		
        mRowId = savedInstanceState != null ? savedInstanceState.getLong(NotesDbAdapter.KEY_ROWID) : null;
    	if (mRowId == null) {
    		Bundle extras = getIntent().getExtras();            
			mRowId = extras != null ? extras.getLong(NotesDbAdapter.KEY_ROWID) : null;
    	}
		
    	populateFields();
		
		Button confirmButton = ComponentUtil.get(this, R.id.confirm);	
		confirmButton.setOnClickListener(new View.OnClickListener() {
		    public void onClick(View view) {
		    	setResult(RESULT_OK);
		    	finish();		    	
		    }
		});		
	}

	@Override
	protected void onPause() {
        super.onPause();
        saveState();
	}

	@Override
	protected void onResume() {
        super.onResume();
        populateFields();
	}

	@Override
	protected void onSaveInstanceState(Bundle outState) {
		if (mRowId != null) {
			outState.putLong(NotesDbAdapter.KEY_ROWID, mRowId);
		}
        super.onSaveInstanceState(outState);
	}

	/**
	 * Popula os campos com as informações da nota
	 */
	private void populateFields() {
	    if (mRowId != null) {
	    	mDbHelper.open();
	        Cursor note = mDbHelper.fetchNote(mRowId);
	        mDbHelper.close();
	        startManagingCursor(note);
	        mTitleText.setText(note.getString(note.getColumnIndexOrThrow(NotesDbAdapter.KEY_TITLE)));
	        mBodyText.setText(note.getString(note.getColumnIndexOrThrow(NotesDbAdapter.KEY_BODY)));
	    }
	}
	
	/**
	 * Salva o estado da nota. Se estiver editando realiza um update, senão, realiza um save.
	 */
    private void saveState() {
        String title = mTitleText.getText().toString();
        String body = mBodyText.getText().toString();

        mDbHelper.open();
        if ((title != null && !title.equals("")) && (body != null && !body.equals(""))) {
	        if (mRowId == null) {
	            long id = mDbHelper.createNote(title, body);
	            if (id > 0) {
	                mRowId = id;
	            }
	        } else {
	            mDbHelper.updateNote(mRowId, title, body);
	        }
        }
        mDbHelper.close();
    }	
}
