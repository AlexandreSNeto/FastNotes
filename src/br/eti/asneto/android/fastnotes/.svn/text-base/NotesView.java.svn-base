package br.eti.asneto.android.fastnotes;

import br.eti.asneto.android.fastnotes.db.NotesDbAdapter;
import br.eti.asneto.android.fastnotes.util.ComponentUtil;
import android.app.Activity;
import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.text.method.ScrollingMovementMethod;
import android.view.Menu;
import android.view.MenuItem;
import android.view.Window;
import android.widget.TextView;
import android.widget.Toast;

public class NotesView extends Activity {
	
	private static final int ACTIVITY_EDIT = 0;
	
	private static final int MENU_EDIT = Menu.FIRST ;
	private static final int MENU_DELETE = Menu.FIRST + 1;
	private static final int MENU_SHARE = Menu.FIRST + 2;
    	
	private TextView mTitleText;
	private TextView mBodyText;
	private TextView mCreationDateText;
	private TextView mLastUpdateDateText;
	
	private Long mRowId;
	private NotesDbAdapter mDbHelper;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
	    
		requestWindowFeature(Window.FEATURE_CUSTOM_TITLE);
	    setContentView(R.layout.note_view);
	    getWindow().setFeatureInt(Window.FEATURE_CUSTOM_TITLE, R.layout.title);
		
	    mDbHelper = new NotesDbAdapter(this);
		
		mTitleText = ComponentUtil.get(this, R.id.title);
		mBodyText = ComponentUtil.get(this, R.id.body);
		mCreationDateText = ComponentUtil.get(this, R.id.creation_date);
		mLastUpdateDateText = ComponentUtil.get(this, R.id.lastupdate_date);

        mRowId = savedInstanceState != null ? savedInstanceState.getLong(NotesDbAdapter.KEY_ROWID) : null;
    	if (mRowId == null) {
    		Bundle extras = getIntent().getExtras();
			mRowId = extras != null ? extras.getLong(NotesDbAdapter.KEY_ROWID) : null;
    	}
    	
    	mBodyText.setMovementMethod(new ScrollingMovementMethod());
		
    	populateFields();
	}

	@Override
	protected void onResume() {
        super.onResume();
        populateFields();
	}

	@Override
	protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putLong(NotesDbAdapter.KEY_ROWID, mRowId);
	}
	
	@Override
    public boolean onCreateOptionsMenu(Menu menu) {
    	boolean result = super.onCreateOptionsMenu(menu);
        menu.add(0, MENU_EDIT, 0, R.string.ctx_menu_edit).setIcon(R.drawable.ic_menu_edit);
        menu.add(0, MENU_DELETE, 0, R.string.ctx_menu_delete).setIcon(R.drawable.ic_menu_del);
        menu.add(0, MENU_SHARE, 0, R.string.ctx_menu_share).setIcon(R.drawable.ic_menu_share);
        return result;
    }	
	
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
        	case MENU_EDIT:
        		editNote(mRowId);
        		return true;
        	case MENU_DELETE:
        		mDbHelper.open();
	            mDbHelper.deleteNote(mRowId);
	            mDbHelper.close();
	            
	            finish();
        		return true;
        	case MENU_SHARE:
        		shareNote();
        		return true;
        }
    	
        return super.onOptionsItemSelected(item);
    }	
    
    private void shareNote() {
    	Intent sendIntent = new Intent(Intent.ACTION_SEND);
		sendIntent.putExtra(Intent.EXTRA_SUBJECT, mTitleText.getText().toString());
		sendIntent.putExtra(Intent.EXTRA_TEXT, mBodyText.getText().toString());
		sendIntent.setType("text/plain");
		
		startActivity(Intent.createChooser(sendIntent, "Compartilhar via:"));
    }
    
	/**
	 * Chama a activity responsável por realizar atualizar a nota
	 * @param itemId
	 */
	private void editNote(long itemId) {
		Intent i = new Intent(this, NotesEdit.class);
        i.putExtra(NotesDbAdapter.KEY_ROWID, itemId);
        startActivityForResult(i, ACTIVITY_EDIT);	
	}

	/**
	 * Popula os campos com as informações da nota
	 */
	private void populateFields() {
	    if (mRowId != null) {
	    	mDbHelper.open();

	    	Cursor note = mDbHelper.fetchNote(mRowId);
	        startManagingCursor(note);
	        if (note.getCount() > 0) {
	        	mTitleText.setText(note.getString(note.getColumnIndexOrThrow(NotesDbAdapter.KEY_TITLE)));
	        	mBodyText.setText(note.getString(note.getColumnIndexOrThrow(NotesDbAdapter.KEY_BODY)));
	        	mCreationDateText.setText(note.getString(note.getColumnIndexOrThrow(NotesDbAdapter.KEY_CREATIONDATE)));
	        	mLastUpdateDateText.setText(note.getString(note.getColumnIndexOrThrow(NotesDbAdapter.KEY_LASTUPDATEDATE)));
	    	} else {
	    		alertSelectedOneNote();
	    	}
	        mDbHelper.close();
	    } else {
	    	alertSelectedOneNote();
	    }
	}
	
	private void alertSelectedOneNote() {
		Toast.makeText(this, R.string.select_one_note, Toast.LENGTH_SHORT).show();
		finish();
	}
	
}
