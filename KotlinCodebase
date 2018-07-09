package com.xxx.xxx

import android.app.ProgressDialog
import android.content.Context
import android.graphics.Bitmap
import android.graphics.Canvas
import android.graphics.drawable.Drawable
import android.net.ConnectivityManager
import android.os.Bundle
import android.os.PersistableBundle
import android.support.design.widget.BottomSheetBehavior
import android.support.v4.app.LoaderManager
import android.support.v4.content.ContextCompat
import android.support.v4.content.Loader
import android.support.v7.app.AppCompatActivity
import android.util.Log
import android.support.v7.widget.Toolbar
import android.view.Gravity
import android.view.View
import android.widget.TableLayout
import android.widget.TableRow
import android.widget.TextView
import android.widget.Toast

import com.google.android.gms.common.data.DataHolder
import com.google.android.gms.maps.CameraUpdate
import com.google.android.gms.maps.CameraUpdateFactory
import com.google.android.gms.maps.GoogleMap
import com.google.android.gms.maps.OnMapReadyCallback
import com.google.android.gms.maps.SupportMapFragment
import com.google.android.gms.maps.model.BitmapDescriptor
import com.google.android.gms.maps.model.BitmapDescriptorFactory
import com.google.android.gms.maps.model.LatLng
import com.google.android.gms.maps.model.LatLngBounds
import com.google.android.gms.maps.model.Marker
import com.google.android.gms.maps.model.MarkerOptions
import com.google.android.gms.maps.model.PolylineOptions
import com.google.maps.android.ui.IconGenerator
import com.xxx.xxx.model.RoutePathInfo
import com.xxx.xxx.model.TripPathInfo
import com.xxx.apolloclient.fragment.F13

import java.text.SimpleDateFormat
import java.util.ArrayList
import java.util.Calendar
import java.util.Date

/**
 * Created by admin on 8/7/18.
 */

class MapRedirectorActivity : AppCompatActivity(), OnMapReadyCallback, GoogleMap.OnMarkerClickListener {

    private var map: GoogleMap? = null

    private var polylineOptions: PolylineOptions? = null
    private var markerLatlng: ArrayList<LatLng>? = null
    private var builder: LatLngBounds.Builder? = null
    private var bounds: LatLngBounds? = null
    private var vehicleNo = ""
    private var receivedTripPath: List<TripPathInfo>? = null
    private var routepath: List<F13.Path>? = null
    lateinit var behavior: BottomSheetBehavior<*>
    internal lateinit var bottomSheet: View
    internal lateinit var list_table: TableLayout
    lateinit var context: Context
    internal var frompage = ""
    internal var lat: Double? = 0.0
    internal var lng: Double? = 0.0

    override fun onCreate(savedInstanceState: Bundle?) {

        super<AppCompatActivity>.onCreate(savedInstanceState)

        setContentView(R.layout.map_redirector)
        Log.v(TAG, "OnCreate")

        frompage = intent.getStringExtra(Config.FROMPAGE)
        vehicleNo = intent.getStringExtra(Config.VEHICLE_NO)

        context = applicationContext
        if (frompage.equals(Config.TripSummary, ignoreCase = true)) {
            receivedTripPath = intent.getParcelableArrayListExtra("path")
        } else if (frompage.equals(Config.RouteSummary, ignoreCase = true)) {
            routepath = RouteSummaryAdapter.DataHolder.getData()

        } else if (frompage.equals(Config.MODULE_SUMMARY, ignoreCase = true)) {
            lat = intent.getDoubleExtra("lat", 0.0)
            lng = intent.getDoubleExtra("lng", 0.0)
        }

        var toolbar = findViewById<View>(R.id.toolbar) as Toolbar
        setSupportActionBar(toolbar)
        supportActionBar!!.setDisplayHomeAsUpEnabled(true)
        supportActionBar!!.setDisplayShowHomeEnabled(true)
        supportActionBar!!.setTitle(vehicleNo.toUpperCase())
        toolbar.setNavigationOnClickListener { finish() }

        builder = LatLngBounds.Builder()
        list_table = findViewById<View>(R.id.list_table) as TableLayout

        val fm = supportFragmentManager.findFragmentById(R.id.mapss) as SupportMapFragment
        fm.getMapAsync(this)

        bottomSheet = findViewById(R.id.bottom_sheet)
        behavior = BottomSheetBehavior.from(bottomSheet)

        behavior.setBottomSheetCallback(object : BottomSheetBehavior.BottomSheetCallback() {
            override fun onStateChanged(bottomSheet: View, newState: Int) {
                when (newState) {
                    BottomSheetBehavior.STATE_DRAGGING -> {
                    }
                }
            }

            override fun onSlide(bottomSheet: View, slideOffset: Float) {

            }
        })

        behavior.state = BottomSheetBehavior.STATE_HIDDEN

    }

    override fun onMapReady(googleMap: GoogleMap) {
        map = googleMap
        map!!.setOnMarkerClickListener(this)
        if (frompage.equals(Config.TripSummary, ignoreCase = true)) {
            ploatPath()
        } else if (frompage.equals(Config.RouteSummary, ignoreCase = true)) {
            ploatPathRoute()
        } else if (frompage.equals(Config.MODULE_SUMMARY, ignoreCase = true)) {
            addmarker()
        }
    }

    private fun addmarker() {
        if (lat != 0.0 && lng != 0.0) {
            val latLng = LatLng(lat!!, lng!!)
            val markerOptions = MarkerOptions()
            markerOptions.position(latLng)
            markerOptions.title(vehicleNo)
            markerOptions.icon(bitmapDescriptorFromVector(this@MapRedirectorActivity,
                    R.drawable.ic_map_marker))
            map!!.clear()
            map!!.addMarker(markerOptions)
            val location = CameraUpdateFactory.newLatLngZoom(
                    latLng, 15f)
            map!!.animateCamera(location)
        } else {
            this.runOnUiThread {
                Toast.makeText(this@MapRedirectorActivity, "No path avilable in the given range",
                        Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun ploatPath() {
        var size = 0
        if (receivedTripPath != null)
            size = receivedTripPath!!.size
        if (size != 0) {
            markerLatlng = ArrayList()
            polylineOptions = PolylineOptions().width(10f).color(R.color.black).geodesic(true)
            for (i in 0 until size) {
                if (receivedTripPath!![i].lat != null || receivedTripPath!![i].lng != null) {

                    val markerLatLng = LatLng(receivedTripPath!![i].lat!!,
                            receivedTripPath!![i].lng!!)

                    markerLatlng!!.add(markerLatLng)
                    builder!!.include(markerLatLng)

                    polylineOptions!!.add(LatLng(receivedTripPath!![i].lat!!,
                            receivedTripPath!![i].lng!!))
                }
            }

            bounds = builder!!.build()

            map!!.addPolyline(polylineOptions)
            val msize = markerLatlng!!.size

            for (i in 0 until msize) {
                if (i == 0)
                    map!!.addMarker(MarkerOptions().position(markerLatlng!![i])
                            .icon(bitmapDescriptorFromVector(this@MapRedirectorActivity,
                                    R.drawable.ic_startflag_map))).tag = i
                else if (i == msize - 1)
                    map!!.addMarker(MarkerOptions().position(markerLatlng!![i])
                            .icon(bitmapDescriptorFromVector(this@MapRedirectorActivity,
                                    R.drawable.ic_stopflag))).tag = i
                else
                    map!!.addMarker(MarkerOptions().position(markerLatlng!![i])
                            .icon(bitmapDescriptorFromVector(this@MapRedirectorActivity,
                                    R.drawable.ic_map_marker))).tag = i
            }
            val width = resources.displayMetrics.widthPixels
            val height = resources.displayMetrics.heightPixels
            val padding = (width * 0.20).toInt()
            map!!.animateCamera(CameraUpdateFactory.newLatLngBounds(bounds, width, height, padding))

        } else {
            this.runOnUiThread {
                Toast.makeText(this@MapRedirectorActivity, "No path avilable in the given range",
                        Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun ploatPathRoute() {
        var size = 0
        if (routepath != null)
            size = routepath!!.size
        if (size != 0) {
            markerLatlng = ArrayList()
            polylineOptions = PolylineOptions().width(10f).color(R.color.black).geodesic(true)
            for (i in 0 until size) {
                if (routepath!![i].lat() != null || routepath!![i].lng() != null) {

                    val markerLatLng = LatLng(routepath!![i].lat()!!,
                            routepath!![i].lng()!!)

                    markerLatlng!!.add(markerLatLng)
                    builder!!.include(markerLatLng)

                    polylineOptions!!.add(LatLng(routepath!![i].lat()!!,
                            routepath!![i].lng()!!))
                }
            }

            bounds = builder!!.build()

            this.runOnUiThread {
                //                    map.clear();
                map!!.addPolyline(polylineOptions)
                val msize = markerLatlng!!.size

                for (i in 0 until msize) {
                    if (i == 0)
                        map!!.addMarker(MarkerOptions().position(markerLatlng!![i])
                                .icon(BitmapDescriptorFactory.fromResource(R.drawable.flag_green32))).tag = i
                    else if (i == msize - 1)
                        map!!.addMarker(MarkerOptions().position(markerLatlng!![i])
                                .icon(BitmapDescriptorFactory.fromResource(R.drawable.flag_red))).tag = i
                    else
                        map!!.addMarker(MarkerOptions().position(markerLatlng!![i])
                                .icon(BitmapDescriptorFactory.fromResource(R.drawable.locn))).tag = i
                }
                val width = resources.displayMetrics.widthPixels
                val height = resources.displayMetrics.heightPixels
                val padding = (width * 0.20).toInt()
                map!!.animateCamera(CameraUpdateFactory.newLatLngBounds(bounds, width, height, padding))
            }

        } else {
            this.runOnUiThread {
                Toast.makeText(this@MapRedirectorActivity, "No path avilable in the given range",
                        Toast.LENGTH_SHORT).show()
            }
        }
    }

    private fun bitmapDescriptorFromVector(context: Context, vectorResId: Int): BitmapDescriptor {
        val vectorDrawable = ContextCompat.getDrawable(context, vectorResId)
        vectorDrawable!!.setBounds(0, 0, vectorDrawable.intrinsicWidth, vectorDrawable.intrinsicHeight)
        val bitmap = Bitmap.createBitmap(vectorDrawable.intrinsicWidth, vectorDrawable.intrinsicHeight, Bitmap.Config.ARGB_8888)
        val canvas = Canvas(bitmap)
        vectorDrawable.draw(canvas)
        return BitmapDescriptorFactory.fromBitmap(bitmap)
    }

    private fun resizeMarker(): IconGenerator {
        //        return Bitmap.createScaledBitmap(BitmapFactory.decodeResource(getResources(), paramInt).copy(Bitmap.Config.ARGB_8888, true), 50, 50, false);
        val iconFactory = IconGenerator(this)
        iconFactory.setStyle(IconGenerator.STYLE_RED)
        return iconFactory
    }

    override fun onMarkerClick(marker: Marker): Boolean {
        if (frompage.equals(Config.TripSummary, ignoreCase = true)) {
            val markerIndex = marker.tag as Int
            if (markerIndex != receivedTripPath!!.size) {
                behavior.state = BottomSheetBehavior.STATE_EXPANDED
                if (frompage.equals(Config.TripSummary, ignoreCase = true)) {
                    createtabletrip(markerIndex)
                }
                return true
            } else
                return false
        } else if (frompage.equals(Config.RouteSummary, ignoreCase = true)) {
            val markerIndex = marker.tag as Int
            if (markerIndex != routepath!!.size) {
                createtableroute(markerIndex)
                return true
            } else
                return false
        } else if (frompage.equals(Config.MODULE_SUMMARY, ignoreCase = true)) {

        }
        return false
    }

    private fun createtabletrip(markerIndex: Int) {

        list_table.removeAllViewsInLayout()
        var row: TableRow

        for (i in 0..4) {

            val t1: TextView
            val t2: TextView
            val t3: TextView

            row = TableRow(context)
            t1 = TextView(context)
            t2 = TextView(context)
            t3 = TextView(context)

            t1.setTextColor(context.resources.getColor(R.color.black))
            t2.setTextColor(context.resources.getColor(R.color.black))

            t1.setPadding(2, 10, 0, 10)
            t2.setPadding(2, 10, 5, 10)

            val lp = TableLayout.LayoutParams(TableLayout.LayoutParams.MATCH_PARENT,
                    TableLayout.LayoutParams.WRAP_CONTENT)
            lp.setMargins(0, 10, 0, 10)

            row.setPadding(0, 5, 0, 5)
            if (i % 2 == 1) {
                row.setBackgroundResource(R.drawable.tablerowbg)
            }

            row.layoutParams = lp

            t1.setTypeface(null, 1)

            t1.textSize = 14f
            t2.textSize = 14f

            val params1 = TableRow.LayoutParams(0, TableRow.LayoutParams.WRAP_CONTENT, 1.0f)
            val params2 = TableRow.LayoutParams(0, TableRow.LayoutParams.WRAP_CONTENT, 1.0f)
            val params3 = TableRow.LayoutParams(0, TableRow.LayoutParams.WRAP_CONTENT, 0.1f)

            t1.layoutParams = params1
            t2.layoutParams = params2
            t3.layoutParams = params3

            t1.gravity = Gravity.RIGHT
            t2.gravity = Gravity.START

            when (i) {
                0 -> {
                    t1.text = resources.getString(R.string.pointdist)
                    t2.text = receivedTripPath!![markerIndex].pointDistance!!.toString() + ""
                }
                1 -> {
                    t1.text = resources.getString(R.string.pointtime)
                    t2.text = receivedTripPath!![markerIndex].pointTime!!.toString() + ""
                }
                2 -> {
                    t1.text = resources.getString(R.string.speed)
                    t2.text = receivedTripPath!![markerIndex].speed + ""
                }
                3 -> {
                    t1.text = resources.getString(R.string.angle)
                    t2.text = receivedTripPath!![markerIndex].angel!!.toString() + ""
                }
                4 -> {
                    t1.text = resources.getString(R.string.timecolon)
                    t2.text = receivedTripPath!![markerIndex].time
                }
            }
            row.addView(t1)
            row.addView(t3)
            row.addView(t2)
            list_table.addView(row, lp)
        }
        behavior.state = BottomSheetBehavior.STATE_EXPANDED

    }

    private fun createtableroute(markerIndex: Int) {
        if (frompage.equals(Config.RouteSummary, ignoreCase = true)) {

            list_table.removeAllViewsInLayout()
            var row: TableRow

            for (i in 0..6) {

                val t1: TextView
                val t2: TextView
                val t3: TextView

                row = TableRow(context)
                t1 = TextView(context)
                t2 = TextView(context)
                t3 = TextView(context)

                t1.setTextColor(context.resources.getColor(R.color.black))
                t2.setTextColor(context.resources.getColor(R.color.black))

                t1.setPadding(2, 10, 0, 10)
                t2.setPadding(2, 10, 5, 10)

                val lp = TableLayout.LayoutParams(TableLayout.LayoutParams.MATCH_PARENT,
                        TableLayout.LayoutParams.WRAP_CONTENT)
                lp.setMargins(0, 10, 0, 10)

                row.setPadding(0, 5, 0, 5)
                if (i % 2 == 1) {
                    row.setBackgroundResource(R.drawable.tablerowbg)
                }

                row.layoutParams = lp

                t1.setTypeface(null, 1)

                t1.textSize = 14f
                t2.textSize = 14f

                val params1 = TableRow.LayoutParams(0, TableRow.LayoutParams.WRAP_CONTENT, 1.0f)
                val params2 = TableRow.LayoutParams(0, TableRow.LayoutParams.WRAP_CONTENT, 1.0f)
                val params3 = TableRow.LayoutParams(0, TableRow.LayoutParams.WRAP_CONTENT, 0.1f)

                t1.layoutParams = params1
                t2.layoutParams = params2
                t3.layoutParams = params3

                t1.gravity = Gravity.RIGHT
                t2.gravity = Gravity.START

                when (i) {
                    0 -> {
                        t1.text = resources.getString(R.string.locncolon)
                        t2.text = routepath!![markerIndex].loc()
                    }
                    1 -> {
                        t1.text = resources.getString(R.string.timecolon)
                        t2.text = routepath!![markerIndex].time()
                    }
                    2 -> {
                        t1.text = resources.getString(R.string.speed)
                        t2.text = routepath!![markerIndex].Speed()!!.toString() + ""
                    }
                    3 -> {
                        t1.text = resources.getString(R.string.angle)
                        t2.text = routepath!![markerIndex].angle()!!.toString() + ""
                    }
                    4 -> {
                        t1.text = resources.getString(R.string.statuscolon)
                        t2.text = routepath!![markerIndex].status()!!.toString() + ""
                    }
                    5 -> {
                        t1.text = resources.getString(R.string.isunauthorized)
                        t2.text = routepath!![markerIndex].isUnAuthorized!!.toString() + ""
                    }
                    6 -> {
                        t1.text = resources.getString(R.string.linkimage)
                        t2.text = routepath!![markerIndex].linkImage()
                    }
                }
                row.addView(t1)
                row.addView(t3)
                row.addView(t2)
                list_table.addView(row, lp)
            }
            behavior.state = BottomSheetBehavior.STATE_EXPANDED
        }
    }

    companion object {
        private val TAG = "MapRedirectorActivity"
    }

}
